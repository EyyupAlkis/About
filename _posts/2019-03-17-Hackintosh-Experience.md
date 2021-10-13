---
layout: post
title: "My Hackintosh Experience"
categories:
  - misc
lang: en
---

Since a lot of people asked me to help them hackintosh their notebooks, I figured it would be interesting to write a bit about my own experience with Hackintoshing.

Before I got the MacBook I use now, I used both a Hackintoshed Windows notebook and desktop computer, so here are my experiences with it.

<!--more-->

## What is a Hackintosh?

A *Hackintosh* is any non-Apple-device that runs macOS.

But: As you might know, macOS, the operating system Apple Macs come with, is intended to only be installed on genuine Apple devices.  
If you inserted a macOS installation Disk into your Windows computer (provided you still have a disc slot 😅), it would fail to boot because it detects it is running on a non-Apple-device.
This mechanism can be circumvented by using special bootloaders like [Clover](https://sourceforge.net/projects/cloverefiboot/) to delude macOS into thinking it is running on a Mac.

Over the years, big communities like [tonymacx86.com](https://www.tonymacx86.com) and [InsanelyMac.com](https://www.insanelymac.com) have formed around the goal of finding ways to Hackintosh their devices, creating tutorials and developing drivers to enable missing functionality.

## How do you 'Hackintosh' a device?

Hackintoshing your device goes like installing any other OS on your computer, except it's just a lot more effort.  
The basic steps go like this:

1. Obtain a macOS-installation-medium (can be done on any real Mac)
1. Install [Clover Bootloader](https://sourceforge.net/projects/cloverefiboot/)
2. Fiddle around with configuration and drivers to get it to boot
3. Use the installation medium to installl macOS onto your computer
4. Boot into macOS
5. Copy over Clover Bootloader and drivers onto your hard drive
6. Again: Fiddle around with drivers to get it to boot without your installation-medium
7. Enjoy macOS!

**But:** I have not completed a single macOS installation without something going awry ...

... and I have done at least *ten* of them.

### Compatibility

Truth is: Some devices are better suited to be Hackintoshed.
Wether a computer makes a good Hackintosh is mostly determined by its driver support.
Components that Apple includes in their own products are a lot better supported than the ones you have to find a community-made driver for.
For a lot of devices there are no drivers at all so you're basically out of luck.

If you happen to own a Desktop computer, you're on the good end:  
Most parts are compatible or can be easily swapped out to something compatible.
There is a great buying guide over on [tonymacx86.com](https://www.tonymacx86.com/buyersguide/building-a-customac-hackintosh-the-ultimate-buyers-guide/).

For notebooks, it is hit-or-miss.  
There is this HP Elitebook Series that is somehow almost 100% compatible and because it is so popular amongst Hackintoshers, there is even an [automated install script](https://www.tonymacx86.com/threads/guide-hp-probook-elitebook-zbook-using-clover-uefi-hotpatch.261719/) that you can use.  
For my own device, the "Acer V5-573G", there is also a [Github Repo](https://github.com/Kaijun/Acer-V5-573G-Hackintosh) that contains all needed drivers.
This has made the installation process a lot easier for me, but it was still at least three days of work to fix all the bugs and get it running.
For other devices, you either are really lucky to find a similar guide for your exact device or you are left with trying out different drivers for the hardware in your notebook - if there even are drivers for it.
In some cases, it can even be neccessary to manually switch out some parts.
For my notebook, I had to order a new WiFi-Module that was also used in earlier Macbooks so it would be compatible.

### Lessons learned & My advice

If you plan to Hackintosh a device yourself, start with a desktop computer.
That way you can get used to the inner workings of macOS and get to know the problems you will face.

Once you decide to make an attempt at a notebook: Take your time. It *will* go sideways.
In a notebook, there are much more individual components compared to a desktop PC like a trackpad, keyboard, speakers, microphone, web cam etc. that will need drivers to function.
Some of the drivers will interfere with each other.
If you go against a wall, try again tomorrow.
Sometimes, it is helpful to note what worked in an installation attempt and try again all over.

Once your hackintosh is fully working, some advice:

- Do not, UNDER ANY CIRCUMSTANCES, update your macOS version without doing a backup beforehand. It can, and will, destroy your whole configuration.
- Your configuration will go sideways as time passes and you tinker around. Prepare to redo your macOS installation once a year.
- Prepare for your Hackintosh not booting at some point. Small changes, like installing some applications, can have a big impact on your drivers.

## What is the experience like?

This last paragraph may seem rather daunting at first.
And to be fair: You should not use a Hackintosh as a critical device - buy a real Mac instead.  
But once you've got your system running, it feels great.

macOS really is a well-designed OS that perfectly suits the needs for creative use cases and software development.
For me, it is the only operating system that can be used for both of my passions:  
For Software Development, I'd like to have Unix-Like environment so I don't need to use VirtualBox all the time. For DJing and Music, Linux is not an option since Linux-Support is basically non-existant in the music production world.
macOS is the only OS that remains!  
By Hackintoshing your device, you get access to macOS without having to shell out a thousand dollars for your computer.

You may ask: If Hackintosh really is so great, why did you buy a MacBook in the end, anyway?  
It really comes down to the time needed to maintain a Hackintosh.
For desktop computers, it might be neglegible.
For my own notebook, at the other hand, it was way more time-consuming and quickly became quite a hassle.   
In the beginning, I enjoyed the tinkering, but after some time, it really got on my nerves.
Once you start to put a price tag onto the time spent on maintenance, that "Apple Tax" start to fade away.

Some of the friends I set Hackintoshs up for still use them, some even started to look into it to be able to maintain it themselves.
Others liked macOS, but weren't happy with missing drivers and incompatibility issues, so they went back to Windows.

## Final Words

If this article shocked you enough to stop considering building a Hackintosh: You're welcome.
But don't let me stop you from trying it out! If you have some time and a spare device laying around, give it a go.

If this article didn't shock you enough or even sparked some interest in building your own Hackintosh, I want to encourage you to do so.  
Personally, I learnt a lot along the way, both in getting to grips with macOS but also about the basic architecture of operating systems and using the command line.
To be fair, I was rather surprised to see how much you could learn just by looking at startup logs to identify driver issues!  
A good starting point for your own journey is [tonymacx86.com](https://www.tonymacx86.com), it has very comprehensive guides for all types of devices.

I hope you enjoyed reading this post!
If you've got any questions or feedback for me, don't hesitate to tweet at me ([@skn0tt](http://twitter.com/skn0tt)).
