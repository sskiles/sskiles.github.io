---
layout: post
author: Shane Skiles
tags: [napkin, thoughts]
---

I'm just tossing out numbers to give me something to think about.

Searching 'human body watts' gives us 100 watts. Even better, it comes from a paper
titled <a href="">The 20 W Sleep Walkers</a> by the National Institute of Health.
More specifically, PubMed Central, National Library of Medicine, 
National Center for Biotechnology Information. ("20 Watt Sleep Walkers" 
would be a great band name.)

Estimates range from 70 watts resting to 300-400 watts during physical exertion.

1 watt = 1 joule per second. Not important for now, but fiddling with time
gives us varying amounts of energy.

Average electricity cost in the US is around 15 cents per kilowatt hour.
1 kilowatt = 1000 watts, 100 watts = 0.1 kilowatts = 1.5 cents per hour.
...times 24 hours = 36 cents per day? That seems way too cheap.

Boston Dynamics <a href="https://support.bostondynamics.com/s/article/Spot-Specifications-49916">
Spot</a> - Typical runtime of 90 minutes. Power supply output 400 watts, 
charging takes ~1 hour, dock charger 2 - 3.5 hours depending on temp.

So 400 watt hours for 90 minutes. Round way up to 300 watts. Hmmm. Not bad, if correct.

<a href="https://www.techbriefs.com/component/content/article/31078-darpa-80-99s-upgraded-atlas-robot-h">ATLAS</a>
- has a 3.7 kWh for 1 hour of 'mixed mission' use.
So, 3700 watts? That's a lot, but not unreasonable considering it's 345 pounds.
Also depends on what 'mixed mission' means.
So, if you had enough robots, you could have 24 hour usage for less than $15 a day.

Hmmm. taking a different approach, just an 
<a href="https://www.ti.com/lit/wp/sprad74/sprad74.pdf?ts=1744778258451">edge 
computing AI camera</a> can run about 2 watts with up to 2 TOPS (not sure if 
If TOPS will play into this later on).

I can't find any good numbers regarding required processing power required for 
image recognition, and autonomous reaction to images. Specifications for autonomous
vehicles are all over the place. 
<a href="https://www.gsaglobal.org/forums/edge-ai-computing-advancements-driving-autonomous-vehicle-potential/">
Global Semiconductor Alliance</a> ranks autonomous vehicles as 
2 TOPS for L2 autonomy, 24 TOPS for L3, 320 TOPS for L4 and 4,000+TOPS for L5. 

...They don't specify what the L levels mean. As of spring 2025 the highest ranked
component I can find comes in at just over 2600 TOPS. Hardware with 40 TOPS is 
considered 
"<a href="https://laptopmedia.com/ai-hardware-performance-rankings/">AI-capable</a>".

Current TOPS per watt reports and estimates vary widely - 25 TOPS/W should be safe 
considering the other calculations going on here.

A typical nuclear power plant produces 1000 megawatts (1 Gigawatt) of electricity and 
takes 5-6 years to build after red tape and permits are done.
The average geothermal power plant produces ~40 megawatts, one of the largest has a
<a href="https://en.wikipedia.org/wiki/List_of_geothermal_power_stations">1500 megawatt</a>
 capacity. They take 5-6 years to build after red tape and permits are done.

 Ooooh, put a huge AI data center in a nuclear power plant without radiation hardening.
 Given how much they hallucinate already, Toxic Avenger AI could be a thing.

 Anyway, back on track...