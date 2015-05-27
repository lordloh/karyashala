---
layout: post
title:  "A Power Supply & Self Powered USB Hub for Raspberry Pi"
date:   2013-09-22 00:00
categories: Embedded Raspberry-pi
permalink: a-power-supply-self-powered-usb-hub-for-raspberry-pi
author: Bharath Bhushan Lohray
---
Of all the applications of the Raspberry Pi, I found running it as a media centre the most utilitarian for my purpose. And why not? It consumes just 5 Watts of power, sports 2 USB ports, an Ethernet port and is nicely topped with a hardware MPEG-4 decoders. The only problems I have had was finding a proper power supply and connecting external portable USB hard drives.

I did come across a few forum posts about people using self powered USB hubs that could back power the Raspberry Pi. However, it did not inspire much confidence in me. Proper USB hub are supposed to only power up the ports to the full 500mA capacity on connecting and enumerating a USB device. In the absence of a USB device, the ports are powered to output no more than 50mA – just enough to enumerate a newly plugged device. At least my [D-Link DUB-H7](http://www.dlink.com/us/en/home-solutions/connect/usb/dub-h7-7-port-usb-2-0-hub) ([amazon][AMZN_USBHUB]) hubs do not back power a device. I was averse to using a micro USB charger adapter and a self powered USB hub as this would block an additional power socket and spill my surrounding with more cable than I can tolerate.

So, to this end, I decided to build myself a power supply setup enough to power my Raspberry Pi and a self powered USB hub enough to power a few portable hard drives.


The things I used in this project were -

* 5V 3A power supply (US$ 4.15) [eBay - US | UK | CA | AU | FR | IT | DE | NL].
* Type-A to micro-B USB cable (US$ 2.99) [eBay - US | UK | CA | AU | FR | IT | DE | NL].
* A bus powered 4 port USB hub (free) [eBay - US | UK | CA | AU | FR | IT | DE | NL].

Type-A to micro-B USB Cable
Type-A to micro-B USB cable.

The type-A to micro-B USB cable was cut in two parts. I used the type-A end to replace the exiting upstream USB plug of the self powered USB hub. The USB hub had an inconveniently  short cable for the upstream plug. The micro-B end was used to power the Raspberry Pi.

A Bus Powered USB Hub
A Bus Powered USB Hub

The self powered USB hub that I used was one that I got as a freebie given out by Fidelity Investments at the job fair. I trust the above picture makes describing it’s uselessness redundant. USB 2.0 specifications guarantee a minimum of 500mA but devices may draw as much as 1.8A. So a 4 port USB hub calls for provisioning at least 2A of current.

A 5v 3A Power Supply
5V 3A Power Supply

The Raspberry Pi documentation claim that the Model-B may consume ~700mA to 1A depending on the devices connected.  [This answer on stack exchange](http://raspberrypi.stackexchange.com/a/1183/1562) reports the current drawn under various usage scenarios very well based on [this forum post](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=63&t=6050&start=50). Considering everything, I decided to use a power source rated at 3A. While this will be insufficient if one chooses to run the Raspberry Pi and the modified hub at the limit, It is sufficient to power an overclocked  Raspberry Pi (950MHz) + Logitech unifying receiver + [Edimax WiFi dongle](http://www.edimax.com/en/produce_detail.php?pd_id=347&pl1_id=1) ([amazon][AMZN_EDIMAX]) + 3 external portable USB drives (amazon – [2TB][AMZN_D2TB] | [1.5 TB][AMZN_D1_5TB] | [1TB][AMZN_D1TB])  + driving Ethernet signals on a 6 foot long CAT 6 cable  (*All tested successfully!*).

 The hub initially had a jack which was de-soldered and removed. Also the barrel connector of the power supply above is unnecessary. It was cut off and  the power source was connected to the micro-B end of the previously cut cable. The point of connection of the two cables was soldered into the PCB of the self powered USB hub at the point from where the power jack was removed.

Modifying the Bus Powered USB Hub to a Self Powered USB Hub
Modifying the Bus Powered USB Hub to a Self Powered USB Hub

I decided to pour some hot glue on the soldered area to provide some strain relief. However, it would not withstand a tug-of-war.

The Raspberry Pi Power Setup
The Raspberry Pi Power Setup

The image above is how my final setup looks. While this was designed for a Raspberry Pi, it can be used with any development board that has a USB port (ex: beagle bone). The block diagram of the modification is shown below.

Raspberry Pi - Power Mod Diagram
Raspberry Pi – Power Mod Diagram

The Raspberry Pi Power Setup connected to the Raspberry Pi.

The Raspberry Pi Power Setup

To keep the hub in place, I decided to stick it to the Adafruit acrylic box ([Amazon][AMZN_PIBOX]) with a double sided adhesive tape.

2013-10-01: I discovered that makezine referred to my post – [see this](http://makezine.com/2013/09/30/usb-hub-powers-the-pi-and-its-peripherals/).

[AMZN_D1_5TB]: http://www.amazon.com/gp/product/B00847V9Z6/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00847V9Z6&linkCode=as2&tag=karyashala-20
[AMZN_D2TB]: http://www.amazon.com/gp/product/B00ARJD56K/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00ARJD56K&linkCode=as2&tag=karyashala-20
[AMZN_D1TB]: http://www.amazon.com/gp/product/B005J7YA3W/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00847V9Z6&linkCode=as2&tag=karyashala-20
[AMZN_EDIMAX]: http://www.amazon.com/gp/product/B005CLMJLU/ref=as_li_qf_sp_asin_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B005CLMJLU&linkCode=as2&tag=karyashala-20
[AMZN_USBHUB]: http://www.amazon.com/gp/product/B00008VFAF/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00008VFAF&linkCode=as2&tag=karyashala-20
[AMZN_PIBOX]: http://www.amazon.com/gp/product/B00C3VQIYQ/ref=as_li_tf_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00847V9Z6&linkCode=as2&tag=karyashala-20
