---
layout: post
author: Shane Skiles
title: Raspberry Pi Zero 2 W
tags: [raspberry pi, zero 2 w, zero 2w]
---

I bought myself a new toy for my birthday this year. I have been wanting to get a
single board computer (SBC) for a while now. I had been looking at something in 
the Raspberry Pi family. They always seemed to have the best support and community.
I finally decided I would get a
<a href="https://www.raspberrypi.com/products/raspberry-pi-zero-2-w/">Raspberry Pi Zero 2 W</a>.
I will lovingly refer to him (yes, he is a he) as Zero from here on out.

Zero is a nice and small SBC with a quad-core ARM CPU, 512K of RAM, built-in Wi-Fi and Bluetooth.
The board itself goes for $15, but I also needed a microSD card and card reader. 
It didn't necessarily need a case, but I wanted to get one. I already had a micro USB
cable and power supply, so I didn't need to buy those. Altogether, I got everything for
less than $50. I bought the board, case and microSD card from 
<a href="https://www.adafruit.com/">Adafruit</a> and the card reader from
<a href="https://vilros.com/">Vilros</a>. 

Raspberry Pi provides it's own 
<a href="https://www.raspberrypi.com/software/">Software Imager</a> to install an OS
on the microSD card. The suggested OS is Raspberry Pi OS (formerly Raspbian), 
which is a Debian-based Linux. Although, you can install other distros like 
Debian, Ubuntu, Arch Linux, etc. I decided to go with Raspberry Pi OS Lite, 
a stripped down no GUI version of the OS. You can set up the network info, 
a user and install the SSH server during the OS install.

Once you get the OS installed, put the card in the board, plug in the micro USB 
for power and away you go. You can SSH into the host name you set up during the install.
It was fun getting to poke around the OS and see what was there. 
I used to run Linux at home a long time ago, so it was nice to back behind 
the keyboard on a new system. The first thing I did was disable as much as I could, 
without making him unbootable or breaking SSH (I did need to reinstall once). 
After a while, I managed to get Zero up and running in less than 70MB of RAM.
When you're only working with 512MB, it just made me feel better to do that.

The next thing I did was to install .NET - a strange first package a Linux install, I know.
But, hey, I'm primarily a .NET developer. I had never installed .NET on Linux before,
so I was curious to see how it would go. I was pleasantly surprised that the installation
was as simple as it was. Microsoft has good 
<a href="https://learn.microsoft.com/en-us/dotnet/core/install/linux">documentation</a> 
on how to install everything.

After that, I installed Git and got that set up. That was a little more involved than I
what I'm used to. After typing that, I realized that I had never set up Git on Linux before.
The last time I used Linux, Git hadn't even been invented yet. Ouch! I shouldn't have
thought about that. Anyway, I got Git set up and was able to clone one of my repositories
and get it up and running. 

From compile to execution in release mode `dotnet run -c release` took about 2 minutes, 
which wasn't that bad considering it was a multithreaded application that opened up 
a few named piped. Between the compile and execution, Zero was using about 130MB of RAM. 
Not bad considering 70MB was used for the OS alone. It also managed to grab just
over 20MB of swap space. I was concerned about how heavy a .NET application would 
be on Zero, but he seemed to be doing just fine. The executable itself was less 
than 100K, but with all the (non system? see below) libraries it came out to 
just over 3MB.

The next thing I decided to try was to compile/publish as a single file .NET for ARM64.
In case you're curious:

`dotnet publish -r linux-arm64 -p:PublishSingleFile=true --self-contained true`

That took a bit of searching to figure out all the flags. The publish took just over 
2 minutes. That resulted in an executable size of 80MB, total memory usage of 120MB, 
and 90MB of swap that got eaten somewhere in the process while running. I'm guessing 
the core .NET libraries are referenced elsewhere in the system for a regular compile
vs the single file publish. That would explain the 3MB vs 80MB difference.

Once I was satisfied with the .NET setup, I moved on to getting Python set up. 
That was a relatively simple `sudo apt install python3` command. 
After an apt update and upgrade, of course. I did manage to get a Python Environment
set up...Maybe? I still don't know what I'm doing with Python. I'm still not sure I
have my virtual environment set up correctly on Windows with Visual Studio. It seems
like I break that periodically. 

I debated on installing Go, I probably will at some point, but not yet. 
The next thing I wanted to install was an editor. The default editor on the Pi is
`nano`, which is fine. But, I wanted something a little more classic. The last time I
used Linux, the fight over `vi` vs `emacs` was still going on. Luckily for me,
`vi` won that battle and it now reincarnated as `vim` and `neovim`. Theoretically,
`neovim` is the way to go now. I decided to give it a go and was pleasantly surprised
that it had syntax highlighting for C# out of the box. I still remembered the basic
`vi` commands, including how to get out of it.

The next couple of days were spent just getting things set up and playing around.
I always need to set up `cls` -> `ln -s /usr/bin/clear bin/cls`. Why do most 
common Unix shell commands get abbreviated to 2 letters except for `clear`!? 
It's nice to have a little *nix environment to play around with. Getting used to
command line `Git` took/is taking a little getting used to. I have always used
it on Windows with a GUI of some sort. Even then I can create merge conflicts
when I'm the only one working on a repo.

A while back I had been doing some work on Azure VMs and I had remote desktop 
set up on my iPad for ease of access. For that work, I had purchased an 
<a href="https://www.google.com/search?q=arteck+hb030b&oq=arteck+hb030b">Arteck HB030B</a>
keyboard. I decided this would be a good chance to break that out again, and if I could
find a good SSH client for an iPad, which I did - 
<a href="https://termius.com/free-ssh-client-for-ipad">Termius</a>, I could combine 
everything together and have a nice little Linux workstation on an iPad. Which I
now have. I love my iPad, but it always annoyed me that there were never any
good apps for development or testing. Trying to develop in the iPad GUI or remoting 
in to another GUI is just kludgy. Being able to just type at a terminal feels natural.
So, a good portion of the interaction I have with zero is actually through an iPad.
Weird.

I still haven't done any "benchmarking" on Zero. That's not really the point of 
having him. I wanted a little *nix machine I could work and play with - 
and he fits the bill. I think if he's under full load he pulls just over a watt,
so power consumption and heat aren't really that big of a concern. If you ever 
want to try something new on one of these, you can swap out microSD cards 
and have a different computer. If you've ever wanted to play with a small SBC, 
a Pi Zero is a fun, inexpensive way to get started.
