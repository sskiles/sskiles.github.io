---
layout: post
author: Shane Skiles
title: "Seeed Studio Xiao ESP32-C6"
tags: [Seeed Studio, Xiao, ESP32, ESP32-C6, MicroPython]
---

Since I have been exploring microcontrollers recently, I decided to try out an ESP32.
The ESP32 is a popular microcontroller series from Espressif, and there are different
manufacturers and board versions based on the ESP32 chips. 
After some research, I decided to go with the ESP32-C6, 
specifically the Seeed Studio Xiao ESP32-C6. 
It comes with built-in WiFi, Bluetooth/Mesh, and Zigbee/Thread support, 
all in a very small form factor.

I've had the board for a week or so now, and I've got it up and running with MicroPython.
I've set up a webserver on it using Microdot that I can use to edit files on the controller
over WiFi and stream system stats. 

As a fun little trick, I set it up to be able to shut off the webserver and make a 
websocket connection to the WebREPL server. If the connection is inactive for X minutes, 
it will close the websocket and restart the webserver. This way, I can connect to it
over WebREPL when I need to, and it will go back to serving the web interface
when I'm done.

Overall, I've probably made more progress on this board than I have on the Pico.
Partially because I am more familiar with MicroPython now, 
but also because the ESP32-C6 has different characteristics.
Like I said, I've only had it for a week, so I may be imagining things, but the ESP32-C6
plays better on the network than the Pico 2 W does. 
I'm not sure why that is, but I'm trying to figure it out.

Part of that may be that the Pico's WiFi stack is not as mature as the ESP32's.
That's an interesting part of working with these microcontrollers, 
the different chips and boards have different capabilities and support levels
within MicroPython. The Pico 2 W came out in November 2024, while the ESP32-C6 
came out in January 2023, so that may be part of it. But, maturity aside,
there are different modules and libraries available for the different boards.

Right now, I'm enjoying working with both boards. I plan on setting them both up
in similar configurations, with web interfaces, WebREPL access, and
streaming system stats. 
I'd like to see how much I can manage the memory and system timings.
I'm still relatively new to Python and MicroPython, so I'm still learning 
about memory management and optimization techniques.

Once I get both boards set up, and I feel like I can comfortably wipe and reload 
the firmware and get them back to a working state, I plan to start trying other languages.
I should just go straight to C/C++, but I may see what else I can find first.
I know there are a few other options out there. 
Some are impractical, like .NET nanoFramework.
Some are missing network support, like TinyGo and FORTH (I really want to try both). 
Others, like Lua and JavaScript, I need to explore further.

I'm not sure what my main goal for the boards is yet. I know I have definitely enjoyed 
learning and experimenting with them. They have been worth the price of admission.
This turned into a meandering post, I should have focused more on the board itself.
Once I get them both set up more fully, I will write a more focused post 
about each board and what I have done with them.
