---
layout: post
author: Shane Skiles
title: "Raspberry Pi Pico"
tags: [Raspberry Pi Pico, MicroPython]
---

On Monday (8/18/2025), I bought a Raspberry Pi Pico 2 W from 
<a href="https://www.vilros.com/">Vilros</a>. 
I had been wanting to get something similar to my Raspberry Pi Zero (or another one) 
for a while, and I finally decided to go for the Pico. I considered going for an ESP32,
but there are just too many options/versions/manufacturers - 
there are a few different Pico manufacturers also, so I went with the one I knew.
And if I decide I want to try an ESP32 later, I can always get one then.

I mentioned Vilros specifically because they are an excellent source for Raspberry Pi 
and Arduino products. I have always had good experiences with them, and they
offer free shipping. The Pico 2 W normally goes for $7.00, they charged $8.61 - 
I added in a $3.99 cable and the total came to was $12.60. How ever they build in 
the shipping and tax, I don't know, but I am happy with the price. They do have other
shipping options, but I go with the free one for anything under $10.

The Pico 2 W is a microcontroller board, not a full computer like the Raspberry Pi Zero.
It has 4MB QSPI Flash, 520KB of RAM, a Dual Arm Cortex-M33 processors running at 150MHz,
and... built-in WiFi and Bluetooth. It is a very capable little board. This will be my first
time working with a microcontroller of my own, so I am looking forward to seeing 
what I can do with it.

Anyway, I got distracted. I ordered the Pico 2 W on Monday, and it should arrive tomorrow
(Saturday). Right now, I'm making my plan. Since there isn't an operating system on it,
you need to jump in feet first. The Pico 2 W supports C/C++ and MicroPython/CircuitPython.
I don't trust my C/C++ skills enough to go that route, so I plan to use MicroPython on it.
I'm choosing MicroPython over CircuitPython because it seems to be more widely used
and it has its own firmware for the Pico 2 W

https://micropython.org/download/RPI_PICO2_W/

That seems like a good place to start. Thonny seems to be the IDE of choice for 
MicroPython development, so I will probably use that for a while anyway. It
should have a REPL for talking directly to the Pico, that should make it
easy to poke around and see what it can do.
Fun fact (for me), since there is no OS, just write `boot.py` and `main.py` files 
and those will run on boot. 

Ideally, I want to be able to connect to the Pico over WiFi, so I can use webREPL.

Theoretically, you can run `import webrepl_setup` and it will set up the webREPL
like this? There doesn't seem to be a lot of information about it, so I will see
what happens when I get there.

```python
import network
import time
import webrepl

def do_connect():
    
    # WiFi SSID and Password
    wifi_ssid = "YOUR SSID"             # YOUR WiFi SSID
    wifi_password = "YOUR PASSWORD"     # YOUR WiFi PASSWORD

    # Wireless config : Station mode
    station = network.WLAN(network.STA_IF)
    station.active(True)

    # Continually try to connect to WiFi access point
    while not station.isconnected():
    
        # Try to connect to WiFi access point
        print("Connecting...")
        station.connect(wifi_ssid, wifi_password)
        time.sleep(10)

    # Display connection details
    print("Connected!")
    print("My IP Address:", station.ifconfig()[0])
    

if __name__ == "__main__":
    do_connect()
    webrepl.start()
```

Anyway, I just wanted to make myself some notes and get things downloaded and ready
as best I can before the Pico arrives. I will post an update once I have it set up and
working.

