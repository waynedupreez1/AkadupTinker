---
title: "Freecad NAS Enclosure Part2"
date: 2020-10-26T14:35:00+13:00
showDate: true
draft: false
tags: ["hardware","nas","cad"]
---

# Description

Okay its been a few weeks since I posted about my adventure of using Freecad to build a Nas enclosure this is part 2.

For the people that has not read the other post the three bullet point summary:
+ I wanted to learn some cad. 
+ I have access to a 3D printer.
+ I am building a Nas.


# Enclosure

As mentioned in the previous post the enclosure is build using 3 sections:

+ ## Core

![NasCorev2](/AkadupTinker/gallery/nas_core_v2.png)

![NasCorePhoto](/AkadupTinker/gallery/nas_core_photo.jpeg)

This contains:

1. 2 x 4TB 3.5 inch HDD's - These will most probably going to be setup in a raid 1 config with the now stable ZFS file system.
2. Raspberry Pi v3 model B - Probably running ubuntu server or arch not sure yet.
3. Sata to USB convertors - Used to convert internal HDD's to USB
4. 2 x PSU's - 24V to 12V for the HDD's and 24V to 5V for the PI
5. 2 x Connectors - One ethernet and one USB used as the 24V input.

Also excuse the dirty table and cup, I need that coffee for the after work activities!

+ ## Inner

![NasCoreInnerv2](/AkadupTinker/gallery/nas_core_inner_v2.png)

![NasInnerPhoto](/AkadupTinker/gallery/nas_inner_led_photo.jpg)

This contains all the LED's.

+ ## Shell

This is still not done but I will get to it.


# Software

+ ## Working

List of softwares currently working.

1. Arch Linux 32bit for RPi v3 -

This was pretty basic. Download the image, burn onto the SD card, pop into Pi. I have to say the arch wiki is probably one of the best resources around.

2. ZFS - 

A little bit more interesting. I could not install this via pacman without compiling all the packages from source. It seems like the pre-built binaries do not exist for armv7h. I also had a fun time figuring out how to auto mount as it seems like the USB to Sata convertors I use take a bit of time to initialize and get going.

3. Transmission - 

For downloading torrents via a web interface this is great. I can now download torrents on other computers and push it to the nas for downloading all via transmissions builtin web interface.

4. Smartctl - 

This runs a service that monitors hard drive health and emails if anything is amis.

5. Pi-Hole - 

A DNS level ad blocker for those annoying youtube ads when using your smart TV. Its not perfect but it helps. This has a few packages that needs to be compiled.

6. Rpi-ws281x-python - 

Python library for controlling the addressable led's.

+ ## Not Done

1. LED patterns - 

I have tested the led's with the test scripts and they seem to work but I need to do some work on generating the patterns I want.

### __And Thats it until the next one.__