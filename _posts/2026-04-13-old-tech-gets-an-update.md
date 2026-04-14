---
layout: post
author: Shane Skiles
title: "Old Tech Gets an Update"
tags: [linux, debian]
---

At the end of last year, I wrote about refreshing an old work laptop with a Linux install
([Old Tech, New Tricks](old-tech-new-tricks)).
Everything was running along pretty smoothly, and I started a little side project that involved some video generation.
I knew the hard drive in the machine was never the fastest, but this project made the point very clear.
I was only generating small segments, so everything was in memory and not swapping, but it was grinding away while the CPU wasn't pegged.
I ran a `smartctl` check on the drive, and the results weren't good.

Most of the errors were relatively minor (some bad sectors should be expected on an old drive), but high "spin up time" and "spin try recount" numbers are not minor issues.
I guess the noises I'd been hearing _weren't_ my imagination.
No biggie, I don't have anything irreplaceable or not backed up on the machine.
I don't even need the machine; it's just for fun/play and side projects/experimentation.
I would miss that.
Maybe I would rescue a "new" old computer somewhere and breathe new life into it.

After a few days of looking around for old machines that might need a new home
for a reasonable price, and not finding anything,
I decided I would go ahead and try to replace the hard drive.
I say "try," but I was fairly confident I could do it.
I just haven't actually done much "hands on" hardware work in the last... it's been a while.
I decided since I was going to be in there anyway, I should go ahead and bump up the RAM from 8 to 16 gigs.

I looked around and found a little Western Digital 240 gig SSD and 8 gigs of no-name DDR3 RAM for less than $100. All new.
I trust used/refurbished electronics mostly, but if something went wrong, I wanted to know that it was probably my fault.
Many people questioned replacing a failing 1 TB drive with a 240 GB SSD.
"Don't you want more space?"
Why would I need that much space?
I would need a _lot_ more memory before dealing with files large enough to warrant that.

Fast forward.
With hardware in hand, I was ready to go.
Tearing everything down went very smoothly.
I decided to tackle the RAM first when everything was disassembled.
I was thinking it would have an 8 gig stick and a free slot.
It had two 4 gig sticks.
Now the machine has 12 gigs of RAM, and I have a 4 gig stick taking up space on my desk.

The hard drive was a little tricky. I'd never messed with a laptop drive before.
Once I got the drive caddy/cage out, and the drive out, getting the new drive in was a trick.
In case you didn't know, those screws are small.
I got the new drive in the cage, tried to put it in, and... it was upside down.
Take it apart, put it together again... upside down again.
I think laptop hard drive cages are like USB plugs, "third time's a charm."

I got everything put back together and only ended up missing one screw.
I knew it wasn't under the battery, and I didn't hear it rattle around while shaking, so I assume it escaped my desk somehow.
I fired it up, got a successful POST, and booted from USB into the Debian install media I had from the last build.
I debated trying something new, but I don't need anything new.
I don't have new hardware. I'm not going to be playing games.
Most popular distros have roots in Debian, and other distros like Fedora or Arch seem like too much work.

The install was quick, smooth, and very, very quiet.
Once everything was installed and it booted under its own power, I breathed a sigh of relief.
I debated for a bit on whether I wanted to run the system as straight Trixie 'stable', Forky 'testing', or Sid.
As far as I'm concerned they are all stable. I've been running Forky and Sid on some Pi Zeros for a bit.
On my last build, I created a bit of a [FrankenDebian](https://wiki.debian.org/DontBreakDebian) by mixing 'stable' and 'testing'.
I gave up on pinning packages after a while and just switched to testing on that build.

This time, I decided I would try just a straight 'stable' system to see how that goes.
It will be an adventure.
The first hurdle was Neovim. I knew I wanted a newer version than what Trixie had.
AppImage to the rescue. All-in-one, no install, single-file application.
Works like a charm.

I do trust the repos for Chrome, VS Code, .NET, and Docker.
I can get by without Chrome; the others are pretty much must-haves.
I had mentioned in a post not too far back ([Home Again](home-again)) that I had been backing up and scripting out configs and such.
This was an excellent chance to put it all to the test.
I must say, it worked out very well — and very quickly.
A new SSD and a bit more memory made the system much snappier.
I kept on thinking, "I'll start this install and go..." — *install done*.

The install scripts and backups worked out very well.
As mentioned above, Neovim was the first target.
One AppImage, a `chmod`, and `mv` later, and `vi` was ready to go.
Scripts for repository installations worked well.
I think I messed up the name of the Chrome version I wanted; that was fixed easily enough.
Pulling down the configs from source control worked well.
I did have a few things I could add to some of my scripts to simplify things,
but I'm actually still refining exactly what I want for some of those.

I can't really think of anything that didn't flow together smoothly.
There was a time or two that I needed to install an extra package, but that was only an `apt install` away.
The biggest change was installing KDE Plasma.
I had been working with XFCE, and most other environments are usually minimal or Gnome.
I figured I had the extra 4 gigs of RAM, so why not treat myself?
It's a nice, clean setup.
I'm sure there are plenty of bells and whistles hidden in there; I just haven't looked yet.
I would love it if I could shave enough off of it to sit under 2 gigs on a blank desktop, but I don't think that's going to happen.
I think I'll have to switch back to XFCE for that to happen.

I did try switching to an XFCE session once, but it hung, and I had to kill it off.
I'm sure it's just a tweak in a DM to get it working, but I don't care enough to do it right now because I'll just switch back.
I've built a couple of nice proof of concept applications that I will probably eventually write about here.
I just have a few things I'd like to tweak in the process to get it right in my head and to align with how I want it done.

Until next time, take a deep breath... and don't let your hard drives fail.

Shane
