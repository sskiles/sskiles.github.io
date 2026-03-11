---
layout: post
author: Shane Skiles
title: "Home Again"
tags: [git, linux, home directory]
---

No, I haven't been away. I just haven't posted in a while. 
I used to post during my wife's live podcasts. 
While she has had a different location for a few weeks, 
that's not my reason for my not posting recently. 
I'm not really sure what the reason is.

I've still been working on little projects, but nothing very interesting.
Actually, one project that was a bit interesting involved setting up a framework which created a local web server on some random port.
That was all fine and good, except it was a headless server.
So I ended up managing to stand up an Nginx instance as a reverse proxy so I could serve from another computer.
As a bonus, I didn't need to worry about remembering the port number.

I should have written a little post about setting up the Nginx server, but I had satisfied my curiosity with what I was working with and moved on.
I nuked that server and started fresh for a new project. 
Which brings us to the title of today's post, "Home Again."

It is always nice to be able to start a new project with a blank slate. 
Knowing where everything came from and all of the dependencies give you a nice calming feeling. 
Even when you fuck up. Correction... especially when you fuck up.
You are the only person to blame. 
If it does or doesn't work, you should know why.

Anyway, since I've started doing a lot of development on little Raspberry Pi Zeros, starting over with a fresh image is super simple.
I almost always set up a few things on a new system - software, configs, and such.
I've decided to try to put a base config into Git so I can just clone my home directory and be ready to go.
I don't quite have everything scripted out yet, but I'm getting there. 
I have a couple of simple scripts to add repositories and auto install a few packages set up, but not everything.
I have some configs set up, but I need to do a bit of testing before I am comfortable with some of them. 
Another thing I have done, is just a general clean up/consolidation of my home directory.
Here is a quick run down of what I have been up to so far (this is mostly for my benefit). Maybe it will give us both some ideas going forward.

The home directory (`/home/sskiles/`) is always a good place to start.
And for a project like this `.gitignore` is probably the best place to start. 
Even a fresh install has trash in it you don't need to back up. 
More importantly, once it's not a fresh install there are things you don't want available in source control. 
I don't plan on making my `home` repository public, but it doesn't need private keys or personal information. The most personal information on there is my `.gitconfig` with my name and email.
The first things in the config are to ignore all files and all directories. From there, I selectively add the wanted files.
Subdirectories become difficult, because you need to un-ignore the directory, re-ignore the files and directories, then un-ignore the file(s) you want.


Recently, I've been using `zsh` a lot, so I've done a fair amount of work on the config (`.zshrc`) there.
I still use `bash`, so that's not going anywhere, it's just not as heavily customized.
The default `.bashrc` for me tries to source a `.bash_aliases` file.
I've changed that to `shell_aliases` and reference that in both  `bash` and `zsh`.
Both shells have their own command history files, and `zsh` has a `zcompdump` for caching/completions. 
I moved all of those to their respective `./cache/<shell>/` folders. 
The `.shell_aliases` file has common shortcuts for editing config files, `clear && ls -al`, directory changes, auto upgrades, command line with too many arguments and such. 
I have one command just to `rm ~/.sudo_as_admin_successful ~/.wget-hsts 2> /dev/null`. I don't need that littering my home! All of that is nicely backed up into Git.

The configs I currently have backed up are for `neovim`, `tmux` and `oh-my-posh`.
For `neovim`, I have a pretty default stub for a `LazyVim` install. 
That took some `.gitignore` trickery to capture it before install, then ignore it again before install. 
Anyway, if that works out as planned the complete config should download and install the first time it is run on a new system.
I will probably add a post-install script to that to do a bit of further tweaking to it.
I'm not a huge `neovim` power user, but I know my way around fine. 
Some of the plugins are nice, mostly the `LSP` functionalities and autocompletes, but I could mostly do without the majority of them.
Anyway, that config has too much. I just don't know enough plugin-foo to set up the things I want (or think I want).
Some of my aliases for editing files are just neovim with no config.

Next on the config list is `tmux`. 
I'm really having second thoughts about this one and I'm thinking about switching back to `screen`. Again, I'm not a big power user of `tmux`, but I do have a few preferences.
- Get the status bar to the top of the screen. `neovim` already sticks one down there.
- If I have to look at the status bar for the terminal, number them starting with `1`. If my keyboard had the `0` on the left, that would be fine. I wouldn't care if I didn't have to look at it.
- Remap the send key to `space` instead of `b`. That's just inconvenient. 
Who chooses <Ctrl>key in the middle of the keyboard</Ctrl> that has no significant meaning to be the main operational, often used key combo for your application?
- Mouse enabled. If I have multiple terminal windows splits open and a mouse, let me use it.

I mostly use `tmux` for the session management. Picking back up where you were from a different computer/session with multiple terminals is very satisfying.
I think I'm considering going back to `screen`, no toolbar to look at, easier prefix key, and I rarely split terminals. That eliminates the entire use of this config. 
Oh well, this way I can use either.

And that brings me to `oh-my-posh`. When I first ran across this program and its prompt themes, I just rolled my eyes. 
Then I found out the prompt themes didn't need to / weren't all neon colors with wing-dings. I managed to find and tweak a nice two line prompt that has:
- `user@host` - I don't know why. I never log in as anything but me. I may eventually start color coding host.
- A check or X mark for the success or failure of the previous command.
- Elapsed time for the previous command. 
- Current day of week, date and time.
- A path format from a theme call `agnoster` which omits intermediate directory structure between the root and ending directories (`~/a/b/c/d/e` becomes `~/.../d/e`).
Very handy for deeply nested directories. If you're that deep in, you already know where you are.
- Source control branch, if the folder is under source control.

And finally as a courtesy, all of the information is cleared after your command is executed, leaving 
only the command and output if any.

Anyway, that's the config so far. I'll give it a test soon on a fresh install and see how it goes. Depending on the results, I'll update here or create a new post.

Shane
