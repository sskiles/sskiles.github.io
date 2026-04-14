---
layout: post
author: Shane Skiles
title: "A Quick Local Jekyll Proxy Setup"
tags: [jekyll, nginx, linux]
---

I haven't worked on the site in a while and I just recently refeshed this computer, so I thought it would be a good time write a quick howto for setting up an existing site locally. 
When I first started this site, I just did what I wanted to locally, then editted as needed after uploading and seeing what it looked like.

If you're going to run a Jekyll site locally, you need to install Ruby and a few dependencies. 
This is a general breakdown of everything needed for most basic Jekyll sites and setting up a local server.

```bash
sudo apt install build-essential ruby-dev zlib1g-dev libffi-dev libssl-dev libxml2-dev libxslt1-dev libcurl4-openssl-dev libsqlite3-dev nginx
```

This gives you compilers, Ruby development headers and libraries along with dependencies for various Ruby Gems. Nginx is for the testing part of this.

Do yourself a favor and set up a local GEM_HOME for Ruby or you'll end up with Ruby and gems scattered all over your system.
Add the following environment variables into your shell of choice.

```bash
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```
Once you have GEM_HOME and PATH configured, this should install them into your user-specific gem       directory, avoiding sudo for gem install.

Next, run:

```
gem install bundler jekyll
```

This installs Jekyll, the site generator, and Bundler, the Ruby's dependency manager.

Next, navigate to you existing site and run:

```
bundle install
```

This will install all of the gems needed to run your site. 

If everything has gone as well, you should be able to run your site now by running the command:

```
bundle exec jekyll serve --incremental
```

This will start up a local server on `http://127.0.0.1:4000` to view your site.
The `--incremental` switch makes any changes to the site immediately visible.
Unfortunately, this site is only local and will only accept local connections.
This makes testing difficult if you are running headless or impossible if you want to access it from another device like your phone or such.
This is where Nginx comes in.

If you aren't familiar with Nginx, look it up. I won't even try to explain its capabilities or complexities.
For the purposes here, we are going to use it as a reverse proxy.
Create a file called `jekyll_proxy.conf` or similar so you know what it is.
This file should contain:

```
server {                                                                                 
    listen 80;                                           
    server_name mymachine.local;       
    
    location / {
        proxy_pass http://127.0.0.1:4000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;                  
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }                                                                                 
   
    location = /jekyll {             
        return 301 /;
    }
}
```

On a Debian system, this file would go in `/etc/nginx/sites-available/`. 
You would then symlink that to `sites-enabled` to keep things nice and clean. 
With this set up and Nginx refreshed, navigating to `http://mymachine.local/` should bring up your Jekyll site the way it would look on your hosting site from any device on your network.
This allows you to test your site on mobile devices and other machines/browsers more easily.

Of course, this is a simple implementation. 
Other Jekyll implementations may not be as friendly as mine is. 

Anyway, I just got done setting this up and thought it was a fun and useful little exercise to document.
Hopefully, someone might this useful.

Shane


