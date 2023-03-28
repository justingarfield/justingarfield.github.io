---
layout: post
title: Windows XP, VirtualBox, Ubuntu 8.10, Amarok, NAS, and a whole lotta music.
date: 2008-11-09 00:00:00 -0500
categories: Linux, Ubuntu 8.10
tags: 64 bit, amarok, itunes, Linux, ubuntu, virtual pc, virtualbox, virtualization, windows media player, windows xp, x64, zune
---
# Who is this article for?

This article was written for users who have large collections of music, which are sick and tired of the long load times, slow response, and horrible interfaces associated with iTunes, Windows Media Player, and the Zune Software (just to name a few of the widespread players). This article is NOT written with any sort of bias towards Apple, Microsoft, and/or any other parties, but rather from personal frustrations, experience, and findings to solve a problem encountered by myself.

# A little background…

I’ve finally centralized all of my music collection onto a new D-Link DNS323 NAS in my home. No longer are the days of switching between four different machines to try and find a certain album or song. With this consolidation came a catalog consisting of over 70GB of MP3s, which amounts to approximately 17,500 songs. I’m not sure if anyone else has tried managing this amount of music in [iTunes](http://www.apple.com/itunes/), [Windows Media Player](http://www.microsoft.com/windows/windowsmedia/default.mspx), or the [Zune Software](http://www.zune.net/en-us/products/zunesoftware/), but you end up with a hell of a mess…It’s tough or takes a while to edit multiple files at the same time to organize your collection, it takes FOREVER to load (especially over the network), and you’re locked in to a product that’s made for your mainsteam, “I don’t want to have to think, thinking is hard.” type of user that requires flashy, stupid people, waving their arms all over the place in a TV commercial.

# The solution?

[Amarok](http://amarok.kde.org/). This music player just kicks ass. It uses SQLLite internally, but can also use PostgreSQL or MySQL as well for its cataloging and statistics. This in-turn makes for a damn fast loading catalog and it even works fast with your catalog attached over the network! It has lots of great features, is easily extensible, and makes editing multiple files and organizing your catalog easier than before!

# The problem?

It runs on Linux and is not native to Windows. I need to be running Windows XP Pro on this machine, because it’s mainly built for my gaming addiction…However, I want to use Amarok at the same time, so I decided to find the easiest way to run Amarok on top of XP…and so the trials began…

# KDE on Windows…aka, crap, I can’t believe I bothered trying.

Well, I first tried the method described on the Amarok download page for Windows, which [resides here](http://amarok.kde.org/wiki/Download:Windows). This required installing KDE on Windows…I figured this would be pretty sweet, since it would pretty much be a seemless desktop integration. Well, I got Amarok up and running in it, but it was an older version, and I couldn’t get it to catalog my music on my network share what-so-ever. The only other option would be to figure out how to compile the latest, stable, version of the code for Amarok and get that into KDE on Windows…Yeah, I’m all set with spending all that time.

# andLinux...and, if you have x64 you’re screwed

Second, I decided to try a solution that uses [andLinux](http://www.andlinux.org/), which is posted on the Amarok forums [here](http://amarok.kde.org/forum/index.php/topic,15735.0.html). Well I can’t say that this does or doesn’t work, because low-and-behold, there is no 64-bit andLinux build, so I couldn’t test this at all.

# MS Virtual PC...better, but still nothing

Third, I decided to just say screw it and install [Ubuntu 8.10](http://www.ubuntu.com/) into a Virtual Machine using [Microsoft Virtual PC 2007](http://en.wikipedia.org/wiki/Microsoft_Virtual_PC). Now MS Virtual PC 2007 only supports 32-bit OS’, so I downloaded the 32-bit image of Ubuntu and gave that a go…Holy crap was the video messed up during the installer, so I pressed F6 and switched into the ‘Safe Graphics Mode’ and was able to install. Once I finally got it up and running, there were still some issues like messed up graphics during boot and no sound. So I tried all the tricks that are hanging around the net for the graphics, and that worked…Then I tried the sound-card tricks, but no go with 8.10…It just won’t see the emulated Sound Blaster 16.

# VirtualBox...F**k yeah! (to the tune of DVDA’s America F**k Yeah!)

So, I searched some more, and then realized that [VirtualBox](http://www.virtualbox.org/) is now available for Windows AND has a 64-bit version too…go figure! So at this point I decided to install VirtualBox and try out Ubuntu 8.10 in a VM through that instead. I also downloaded the 64-bit ISO of Ubuntu at this point, since there’s no point in not using my machine’s 64-bit capabilities. I fired up the VM and got an error saying that the 64-bit OS wasn’t supported (can’t remember the exact wording)…The answer to this problem? Go into the Settings of the VM and then to the Advanced tab. You’ll see a property called Enable VT-x/AMD-V, check this box off, then go to the Audio properties and select the ICH AC97 controller and the Windows DirectSound Host Driver, and you’ll be good to go.

You’ll probably still see some whacky video issues, but just disregard them for now. After installing Ubuntu, make sure you run the Update Manager (System -> Administration -> Update Manager) Once that’s done, open up a new Terminal (Applications -> Accessories -> Terminal) and type in sudo aptitude install amarok and you’re on your way to a much faster and enjoyable music-playing and cataloging experience!

One remaining thing you’ll want to do after getting your Ubuntu Guest OS up and running, is to install the Guest Additions for a more seamless experience. To do this click on the Devices menu within the window the OS is running in, then click on Install Guest Additions… This will mount a new image to cdrom0, which you’ll see appear on the desktop of the Ubuntu machine. Ignore the error about not being able to run it, and open a new Terminal (Applications -> Accessories -> Terminal) Then go to the mounted cdrom image by typing cd /media/cdrom0 Next, if you’re running 64-bit, type sudo ./VBoxLinuxAdditions-amd64.run otherwise type sudo ./VBoxLinuxAdditions-x86.run Enter your password, and then reboot when it’s done installing. Done!

One AMAZING difference I found between running Ubuntu on MS VPC vs. VirtualBox was about a 4x improvement in overall performance! It loaded 4x faster, it compiled and installed the same updates approximately that much faster, and there was no ‘visual lag’ when attempting to move the mouse around or click on a menu or button.

# Other

Sound issues? [Check here](http://ubuntuforums.org/showthread.php?t=866965).

Need to mount a remote share on your NAS? [Check here](https://help.ubuntu.com/community/MountWindowsSharesPermanently).
