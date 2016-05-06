---
layout: post
title:  "Has AES / RSA been cracked?"
date:   2015-08-24 00:00
categories: [opinion, technology]
tags: [encryption]
slug: has-aes-rsa-been-cracked
permalink: /:title/
comments: true
author: Bharath Bhushan Lohray
---
So, has the AES or the RSA been cracked?

I don't know. I am not going to present theories in this post explaining probabilities and mathematics of how (im)probable it is. What I am going to do is point out certain events from history that would highlight the patterns of governments using cryptography , cryptanalysis and how enviously these information were protected. Based on this, I leave it to you to decide if the AES / RSA has been cracked...

Lets start with the assumption that the AES / RSA has not been cracked. To support this postulate, we have -

1. A lot of technology news about NSA putting it's weight behind extending surveillance programs. See: [Deeplinks blog:NSA-spying](https://www.eff.org/nsa-spying),  [XKeyscore](https://en.wikipedia.org/wiki/XKeyscore), [PRISM](https://en.wikipedia.org/wiki/PRISM_(surveillance_program)), [etc](https://en.wikipedia.org/wiki/Global_surveillance).

2. NSA strong arming technology corporations to disclose user data, especially decryption keys. See:  [Lavabit](https://en.wikipedia.org/wiki/Lavabit).

So, if the NSA could crack the AES / RSA, why would they have to bully companies into providing decryption keys? And why bother with side channel attacks?

Now let me point out a few historic events -

1. **[The Babington Plot](https://en.wikipedia.org/wiki/Babington_Plot)** - Mary the queen of scots was given a cypher by the agents of Queen Elizabeth. She used the cypher not knowing that it was being intercepted and decrypted. Eventually she was caught making a correspondence that accounted for treason and her eventual execution. Moral of the story: Don't blindly trust encryption. Especially when they are provided by the NSA.

2. **[Cracking the Enigma](https://en.wikipedia.org/wiki/Cryptanalysis_of_the_Enigma)** - The German World War II cryptography was formidable. The first scheme to crack it was developed by a Polish mathematician called [Marian Rejewski](https://en.wikipedia.org/wiki/Marian_Rejewski). The Polish lost to the Germans early on and handede over their results to the British in the hopes that the British would have the necessary resources to carry the work forward and act against the interests of the [Third Reich](https://en.wikipedia.org/wiki/Nazi_Germany). The British dedicated towards this effort at [Bletchley Park](https://en.wikipedia.org/wiki/Bletchley_Park). The work of the British team lead to a system of automating the cryptanalytic process. Especially the contributions of [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing). However, in spite of gathering intelligence about German naval activities, the British choose to ignore some intelligence so as not to rouse the German suspicion that the enigma cypher was cracked. See: [The Code Book - Simon Singh](http://amzn.to/1EQ3yXZ) and [The Codebreakers - David Kahn](http://amzn.to/1PLtVET). Some tales go as far as to say that Winston Churchill allowed British cities to be bombed over letting the Germans suspect and overhaul their cryptographic systems. This exhibits the extent to which governments are willing to go to keep the compromise of a cryptography secret.

3. **[Zimmermann Telegram](https://en.wikipedia.org/wiki/Zimmermann_Telegram)** - The Germans attempted to win over Mexico to the [Axis side](https://en.wikipedia.org/wiki/Axis_powers), by promising them Texas, Arizona and New Mexico in return for military support in North America, if the United States did not remain neutral and joined the World War II. The message was encrypted and sent over a transatlantic cable that passed through a relay station in England. After the British managed to decypher it, they passed it on to the United States government to convince them to join the [Allied cause](https://en.wikipedia.org/wiki/Allies_of_World_War_II). To cover up the decryption, the British created a cover story of bribing, stealing and decyphering the telegram in Mexico.

These historic events bring out how jealousy governments guard the knowledge of compromised cryptographic systems. So, if the AES / RSA was indeed crackable by the NSA, they are not likely to publish a paper on it. Moreover, the NAS is a counterintelligence agency. Spreading misinformation is very much in their purview. Much of this may sound like conspiracy theory. But the morals are irrefutable.

If you indeed had an uncrackable communication system, it still does not help if the person at the receiving end is negligent or prevent him / her from ratting you out.
