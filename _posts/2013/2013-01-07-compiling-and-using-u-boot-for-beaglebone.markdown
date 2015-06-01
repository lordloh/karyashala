---
layout: post
title: "Compiling and using U-Boot for BeagleBone."
date: "2013-01-07 23:21"
category: [Linux,embedded]
tags: [tips-tricks, BeagleBone, u-boot]
permalink: compiling-and-using-u-boot-for-beaglebone
author: Bharath Bhushan Lohray
---

The [BeagleBone]( https://web.archive.org/web/20140209172705/http://beagleboard.org/bone) ships with a micro SD card that contains an [Ångström Linux distribution](http://www.angstrom-distribution.org/). The images may be found and downloaded from the Ångström website. This image works perfectly out of the box.

The problem I faced was how to get the BeagleBone load a kernel or a baremetal program from a TFTP server and execute it and to get [U-Boot](https://web.archive.org/web/20140209172705/http://www.denx.de/wiki/U-Boot/WebHome) to load a  program from the TFTP server at boot without the user having to manually execute the boot commands each time


In the past, kernel and baremetal development on the [BeagleBoard](https://web.archive.org/web/20140209172705/http://beagleboard.org/hardware) (rev C3) required me to pull out the SD card each time I compiled something and reboot the BeagleBoard. Pulling out and reinserting the card into the slot about 3 or 4 times an hour and burning an image on to it was a development bottleneck and not to mention, I risked mechanically damaging some delicate parts. The projected ended prematurely when the mechanical stress while handling the board resulted failures that led the I2C operations to go dead. A ~$150 hit on my exchequers.

When I bought the [BeagleBoard-xM](https://web.archive.org/web/20140209172705/http://beagleboard.org/hardware-xm), Pandaboard, BeagleBone, Raspberry Pi, Atlys and Zed Board, all of which had an ethernet ports on them, it made sense to load the kernel or baremetal program over the LAN to mitigate the risk of physical damage and unthrottle the development bottleneck of writing images to an SD card for each modify-compile iteration.

Image Distribution
==================

To dish out the kernel images to the development boards, A DHCP and a TFTP server were setup. The DHCP server is configured to assign a fixed IP address, to the MAC address corresponding to the BeagleBone. The configuration corresponding to the BeagleBone’s MAC address also specifies the IP address of the TFTP server and the file name of the kernel image file that it should download from that TFTP server. Specifying this for the specific MAC lets various hosts (x86 PC / ARM / MIPS / PowerPC ) get their architecture specific images.

U-Boot
======

U-Boot or Das u-Boot is an openSource universal boot loader distributed under the GNU GPL v2 for embedded ARM, PowerPC and MIPS systems. U-Boot needs to be configured  for a specific architecture and board before it can be built. U-Boot is a stage 2 boot loader (read more) which is loaded by the stage 1 boot loader. The Stage 1 boot loader is known as the x-loader. The x-loader executable binary is in a file called MLO. Most ARM processors have some basic ROM code that is enough to load and execute a file called MLO from the SD card or NAND.

The x-loader may sometimes be found as independent projects for specific boards or independent of U-Boot. For example [TI’s x-loader](http://gitorious.org/x-load-omap3) which attempts to make x-loader independent of U-Boot. An attempt to combine MLO and u-boot.img from different releases resulted in an unbootable system.

Compiling U-Boot
================

Pre-compiled binaries for various boards may be download from various sites such as the Ångström distribution. However, if one chooses to compile their own U-Boot, the sources may be obtained from [here](ftp://ftp.denx.de/pub/u-boot/) or from the git repository at `git://git.denx.de/u-boot.git`.

## Starting clean:

Default

1
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean


## Configuring for an Architecture:

Configuring for the BeagleBone

```
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- am335x_evm_config
```

Configuring for the BeagleBoard-xM

```
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- omap3_beagle_config
```


Configuring for the PandaBoard-ES

```
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- omap4_panda_config
```

## Building U-Boot

```
$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-
```


This results in `MLO`, `u-boot.bin` and `u-boot.img` being generated. These files may be copied into the FAT partition of the SD Card or flashed into the NAND.

## U-Boot Boot Sequence

On power up or reset, the x-loader loads U-Boot and hands off control to it. The U-Boot waits for a short while for the user to interrupt the boot and then proceeds to execute the commands in the environment variable bootcmd.

The default boot sequence for the am3335x (BeagleBone) is -


```
  mmc dev ${mmcdev};
  if mmc rescan;
  then
    echo SD/MMC found on device ${mmcdev};
    if run loadbootenv;
    then
      echo Loaded environment from ${bootenv};
      run importbootenv;
    fi;
    if test -n $uenvcmd;
    then
      echo Running uenvcmd ...;
      run uenvcmd;
    fi;
    if run loaduimage;
    then
      run mmcboot;
    fi;
  fi;  
```


This initializes the SD card, loads `uENV.txt` to memory, imports the contents from the memory to environment space, loads `uImage` and boots it. Various words seen in the script above are actually variables. `${variableName}` is substituted by the value in the variable. Commands in variables may be executed as


```
run variableName
```

To change the boot sequence, the board configuration files have to modified and U-Boot has to be recompiled. For the BeagleBone, this file is `include/configs/am335x_evm.h`. An extract from the `am335x_evm.h` source file is shown below -

```
  #define CONFIG_BOOTCOMMAND \
    "mmc dev ${mmcdev}; if mmc rescan; then " \
      "echo SD/MMC found on device ${mmcdev};" \
        "if run loadbootenv; then " \
          "echo Loaded environment from ${bootenv};" \
          "run importbootenv;" \
        "fi;" \
        "if test -n $uenvcmd; then " \
          "echo Running uenvcmd ...;" \
          "run uenvcmd;" \
        "fi;" \
        "if run loaduimage; then " \
          "run mmcboot;" \
        "fi;" \
    "fi;" \
```

To change the boot sequence to load a file from the TFTP server and boot it with the ext4 file system on the SD card as the root, I modified the source as below and recompiled U-Boot to behave as desired.

```
   fine CONFIG_BOOTCOMMAND \
        "mmc dev ${mmcdev}; if mmc rescan; then " \
               "echo SD/MMC found on device ${mmcdev};" \
               "if run loadbootenv; then " \
                     "echo Loaded environment from ${bootenv};" \
                     "run importbootenv;" \
               "fi;" \
               "if test -n $uenvcmd; then " \
                     "echo Running uenvcmd ...;" \
                     "run uenvcmd;" \
               "fi;" \
               "if dhcp ${loadaddr}; then " \
                     "run mmcargs;" \
                     "bootm ${loadaddr};"\
               "fi;" \
         "fi;" \
```

I would like to conclude this entry by touching on an alternate boot loader called [barebox](http://barebox.org/). barebox is a boot loader that was derived from U-Boot and is also distributed under GNU GPL v2. Barebox scripts and commands are very Similar to U-Boot and a knowledge of U-Boot can get one started on barebox very quickly. Barebox also provides some good features like a boot menu.

Barebox uses [`kconfig`](/http://en.wikipedia.org/wiki/Menuconfig) can be configured by -

```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig
```

Which lets the user tweak a lot of features before building the MLO & u-boot. While BeagleBone is not supported by barebox yet, BeagleBoard & Pandaboard are. There are a  few patches floating around that claim to put in support for the AM335x processor, but none of them worked out of the box on the current (2013-01-06) git copy.
