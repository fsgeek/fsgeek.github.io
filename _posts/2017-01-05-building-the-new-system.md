---
id: 106
title: Building the New System
date: 2017-01-05T03:35:03+00:00
author: fsgeek
layout: post
guid: https://fsgeek.ca/?p=106
permalink: /2017/01/05/building-the-new-system/
categories:
  - Hardware
---
In my prior life I&#8217;d had an opportunity to assemble some awesome tools for my work.  This included both a great desktop (workstation) system as well as a good laptop system as well.   Even at four years old, the desktop system was terrific: dual Xeon processors with 48GB of RAM (24GB each CPU).  Each E5 CPU had 6 cores, with hyperthreading.

I started with a small Intel NUC system &#8211; dual core i7-5557U with 16GB of RAM, a mechanical keyboard, a 4K2K UHD monitor and gaming mouse, with a Samsung SSD.  Good performance and it allows me to dual boot Windows 10 (1607) and Linux Mint (Ubuntu 16.04).  I needed something portable so I ultimately settled on a Microsoft Surface Book.  I&#8217;m still getting used to it, but I do find the detachable screen/tablet useful.

As I started my current research project (more on that later), I concluded that I&#8217;d like to do some work with [TSX](https://en.wikipedia.org/wiki/Transactional_Synchronization_Extensions), Intel&#8217;s new hardware transactional memory support.  I had no problems downloading and running one of the packages that demonstrates the use of TSX and I was pleasantly surprised to find that my Surface Book actually has full TSX support, which is awesome.

Upon further thought, I concluded that I still needed to have my workstation capable as well &#8211; and unfortuantely the NUC system doesn&#8217;t have TSX support.  So I looked at a variety of options, along with other potential needs for my research and concluded that what made the most sense was to build a new workstation.  It took me a couple of days looking at various options to settle upon my current  solution: I&#8217;m building another workstation.

The base of my workstation is an Asus Z10PE-W8 motherboard.  This is a dual socket Intel 2011-3 motherboard.  I&#8217;ll be using dual E5-2603 v4 CPUs (6 cores each, no hyperthreading).  The work I&#8217;m doing isn&#8217;t CPU bound.  Rather, for TSX testing/work I want a machine that will exhibit real memory contention.  This is a NUMA architecture system, so each CPU will have its own dedicated memory channel.  Hyperthreading is actually bad for TSX (since the threads share CPU resources) though I suppose there may be failure modes I won&#8217;t see (but the Surface Book has SMT, so I can test it out there as well).   These are the low end CPUs, but they have TSX-NI support (according to the Intel ARK page, let&#8217;s hope it&#8217;s right).  I&#8217;ve paired it up with 64GB of RAM (2x32GB) so I&#8217;ll have plenty of memory _and_ plenty of options for expanding the memory in the future.  While I&#8217;d like to add NVDIMMs as well, that&#8217;s outside my budget at the moment.  The option is still there, though&#8230;

For storage, I&#8217;ll put a SANDisk 1TB m.2 SSD on the system.  As far as I&#8217;m concerned, SSD is the best choice for storage on a high performance workstation these days, though there are faster options albeit at a much higher price point.  Rounding that out with an AMD FirePro W4300 workstation graphics card (**four** DPI ports!) and a Phanteks case (this _is _an SSI EEB motherboard, so there aren&#8217;t a huge number of case options!)  plus some Noctua CPU fans and I have a solid workstation.  Ironically, it&#8217;s really just an updated version of what I built four years ago.

I&#8217;ve settled on a Das Keyboard Model S &#8211; a nice mechanical keyboard with excellent reviews.  I&#8217;m definitely hard on my keyboards.  I&#8217;ll use my existing Acer monitor for now, though I&#8217;m shopping now for a second monitor, as I know from prior experience that multiple monitors are great for productivity.

Parts started arriving today.  Once I have everything, I&#8217;ll put it all together and make sure that it all works.  The case will be mostly empty for the time being &#8211; the case is huge because the motherboard is itself huge, but I&#8217;m not going to populate it with storage yet.  I&#8217;m a fan of the HGST Helium drives &#8211; for mechanical drives they are pretty amazing but for what I&#8217;ll be doing in the near-term I don&#8217;t expect to need a big storage device.  Still, I can see how it will be useful moving forward.

Look forward to reporting on how it all comes together.