---
title: "Freecad NAS Enclosure Part1"
date: 2020-10-11T08:00:00+13:00
showDate: true
draft: false
tags: ["hardware","nas","cad"]
---

# Description

I love to tinker with things, generally my computers and sometimes when you tinker you tend to screw up and loose data, sometimes very important data like your wedding photos and everything else. 

Yes you read correctly I lost my wedding photos, all of it. This happened whilst moving from linux to windows and not backing up properly, yeah I know pretty stupid.

Fortunately with some more tinkering and running some recovery software for a few days I managed to recover all my photos.

I few takeaways from this experience:

1. Encryption is super important for sensitive data - I managed to recover data from the drive that was a few years old and was deleted in different operating systems. Data tend to stick around on disks.

2. Backup, backup and do another backup and perhaps another - I had an independent backup on an external hard drive and I still lost data. Something odd happened were it seems like it did not do a recursive copy of all files within folders.

3. Remove backup hard drives from tinker PC - Yes build some sort of Nas to house all the drives. And this is the point of this post and is discussed throughout.

# 3D modeling

I have access to a 3d printer which would do nicely to make an enclosure.

I have worked with Solidworks perhaps 10 years ago doing some really basic sheet metal stuff, but never really took this up again. Cad modeling in my opinion is a fairly useful skill and any engineer should be able to do some of the basics. I see this as similar to an electronic hardware engineer not knowing anything about software doable but, easier to design if you do.

While looking around I found a few modeling packages though most of those are payed for which is fine if its something you are going to be taking a bit more seriously. In my situation I chose freecad a free and open source cad modeler it definitely has issues with random crashes and other quirks but it seems pretty capable. I watched a few Youtube videos and was good to go.

# Nas Enclosure

Now most enclosures are cubes, I mean this makes sense its probably the best seeing that everything I am planning to but into the Nas is also cubes, though its boring. I decided to do something different, the nas will have three sections:

### Core

![NasCorev00](/AkadupTinker/gallery/nas_core.png)

I have allowed for:

1. 2 x Hdd's - These will most probably going to be setup in a raid 1 config with the now stable ZFS file system.
2. Raspberry Pi controller - Probably running ubuntu server or arch not sure yet.
3. Sata to USB convertors - Used to convert internal HDD's to USB

### Inner

![NasInnerv00](/AkadupTinker/gallery/nas_inner.png)

I thought, would it not be cool if by looking at some LED patterns you could figure out if someone is currently uploading or downloading data from/to the nas? 

So over the core I will have the Inner section which will contain a bunch of addressable led's.

### Shell

Finally I will have a shell. Covering the LEd's.


# Closure

I am planning on starting 3D printing parts next week so should have something in the next few weeks. I am sure to do another post with the final product.


### __And Thats it until the next one.__