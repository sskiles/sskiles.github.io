---
layout: post
author: Shane Skiles
title: "Old Tech, New Tricks"
tags: [C#, VS Code, Linux, Debian]
---

I've had an old laptop sitting around gathering dust for a few years now, and I decided to throw Linux on it and give it a breath of fresh air. The laptop itself is a 2015-ish Inspiron with 8 gigs of RAM and a 1 TB hard drive. It had a Windows 8 installation on it along with Visual Studio 2013/15/17/19, a couple of different VPNs baked into its network stack, and years of cruft that development and experimentation bring. I remember using it on a video call and hearing remarks about how slow it was in 2019. That was one of the last times it was used.

After firing it back up (and remembering the password!), I did a once-over of everything on the system to verify data backups. Then I had to decide on what distro I wanted to install.

- I didn't want anything cutting edge; I don't need it, and the system probably couldn't handle it.
- I didn't want anything designed for limited systems; the specs on the system weren't that bad.
- I didn't want a "difficult" distro; I remember spending days recompiling a Gentoo system—I'm too old for that now.
- I just wanted something that worked.

I decided to try Ubuntu. The install was fine, as expected. Then I started installing and updating tooling. I relied on web documentation for some of the install instructions. Well, I guess that was all on me. Mixing `apt` and `flatpak` just didn't play well. Fix one thing, then break another. Combine that with an annoying mouse jump (bad trackpad, I'm guessing), which came back persistently—I was done. I was going to wipe clean and start again.

This time, I decided to put Debian on it. I'm familiar with Debian; I love it for server systems. I have a flavor of Debian on my Pi Zero. Good, solid choice. Let's see how it does as a desktop. The install was smooth. It gave a choice of desktop environments; I chose Xfce. After finishing the setup and getting logged in, I just played around with the UI for a bit (no unexpected mouse jumps), and all seemed good. Now it was time to start installing everything needed to make it a home.

Learning from my Ubuntu experience, I made it a point to only install with `apt`. No `dpkg`'ing `.deb`s—it needed to have a repository I could add to my apt sources. This worked out well. Chrome, VS Code, and GitHub CLI all have their own repositories. Most other things are already in apt or are online/web services. Everything went incredibly smoothly. No dependency errors; everything just worked.

This APT has Super Cow Powers.
```sh
sskiles@office:~$ apt moo
                 (__)
                 (oo)
           /------\/
          / |    ||
         *  /\---/\
            ~~   ~~
..."Have you mooed today?"...
```

After getting everything installed, it's a whole other task to get everything logged in and authenticated. Actually, that was more of a pain than actually installing everything. My phone hadn't had that much of a workout getting texts, emails, and whatever other mobile device MFA alerts I had hooked up. It takes a couple of days for you to figure out/remember those things you only do once on a new system.

It's been a fun little adventure getting everything set up and configured and finding new things along the way. One package I found while looking up some UI tricks was Conky. That is a fantastic, highly configurable system monitor that will just run windowless on your background. I'm sure I'll find more tools, tips, and tricks in the near future.

Currently, I am very pleased with the setup I have installed right now.
- I am currently typing this in VS Code.
- Chrome is running with ~10 tabs.
- Spotify is playing in its own window.
- Thonny is online and connected to a microcontroller.
- A couple of terminals are open (one is testing setting up and tearing down `docker` services).
And the system is running in just under 5 gigs of RAM, and CPU load is around 10%. Everything is rock solid and snappy. I'm surprised Chrome isn't eating that much memory by itself. The only real "optimization" I've done so far is to disable CUPS. Other than that, I still need to get myself really familiar with VS Code, and then it'll feel homey. It won't be a daily driver, but more of a "hobby hauler."