+++
author = "Imad Alihodzic"
title = "The Tragic Security Implemented in Building Management Systems"
date = "2024-01-17"
description = "A clear example of why you need to force users to change the default password!"
tags = [
    "security",
    "IoT",
]
categories = [
    "authentication",
    "security",
]
url = "/blog/shodan-lantronix-dork"
series = ["Security"]
aliases = ["security"]
+++

I've been debating whether or not I should write this blog post, as it’s another one of those posts which talks about painfully simple, yet damaging, cybersecurity failures. However, it’s so simple that any ill-meaning party can figure it out themselves with a small amount of research and time, and plenty of other research already exists on this topic. Also, my blog gets almost no views, so I've decided to go ahead. 

<!--more-->

More specifically, this will be a post which focuses on BMS - building management systems.

## What are Building Manegemnt Systems?

BMS are computer-based control systems, installed in buildings to manage and monitor the mechanical and electrical equipment such as ventilation, lighting, power, and fire systems. They are installed in most new buildings (and many old ones), as they can automatically and manually be optimized for efficiency, and comfort. Essentially, a BMS functions as the brain of a building, ensuring all systems operate together harmoniously and efficiently.


## Why care about this?

Naturally, companies quickly realized that if they equipped such systems with network ethernet jacks, they would enable customers to connect to the BMS remotely, using it to in turn control the building’s systems via a protocol BACnet or LonTalk. 
Examples of these products include the Swegon Gold/SuperWise BMS controllers, which are commonly used in Nordic countries. Other examples include the Tridium Jace controllers which run Niagra - with this sort of system primarily being used in the USA. 

{{< img src="/post/bms-security-failiures/superwise.png" alt="Picture of Swegon Superwise router" class="horizontal-class-two" caption="A Swegon Superwise router for connecitng to gateways">}}

## The issue 

The problem, however, is that many of these systems are woefully insecure. So much so, in-fact, that one does not need to do anything more than search up the default username and password in google!

If we, for example, decide to search up the very complex and evolved search string of `swegon superwise password`, we can open the first result, search for the password, and have it helpfully shown:

{{< img src="/post/bms-security-failiures/swegonPass.png" alt="Picture of Swegon default passwords" class="horizontal-class-two" caption="The ever so helpfully displayed passwords, given out by Swegon themselves.">}}

In regards to the Tridium/Niagara devices, the solution is sadly not *as* straight-forward. Due to the fact that the software was licensed out to a large number of other manufacturers, the default password on these devices varies. Also, this problem already being known for a long while (see for example this [NIST CVE](https://nvd.nist.gov/vuln/detail/CVE-2017-16744), [this POC](https://github.com/GainSec/CVE-2017-16744-and-CVE-2017-16748-Tridium-Niagara), or [this FBI warning?](https://cyberscoop.com/fox-protocol-fbi-warning-port-1911-ics-security/)), some manufacturers have issued updated forcing systems to change the default credentials. 
However, many manufacturers haven't, and of those that have, few customers have upgraded their systems. With a bit of online sleuthing on forums such as hvac-talk.com (seriously, this is a great forum for finding all sorts of info on HVAC and BMS systems), passwords can be found. 

## The Impact

So how large is the impact of these? Well, running simple searches on sites such as [Shodan](https://shodan.io) or [Criminal IP](https://criminalip.io) reveals many results. 
In the case of Swegon devices, these results will, alarmingly, often lead to controllers in relatively modern/new buildings, which are of very large/public nature. More alarmingly, the majority of these *may or may not* use the default credentials printed in the Swegon user guide! 

Once logged in, any ill-meaning party will be greeted by a UI which allows them to do any number of things. These include getting access to the blueprints/plans of the building (as they are often used when mapping the different systems in a building, statistics and real-time information on information ranging from room occupancy, to energy usage and ventilation fan speed(s). 

{{< img src="/post/bms-security-failiures/swegonDemo.png" alt="Image of the SuperWise UI (note image is taken from the Swegon website, not an actual running system)." class="horizontal-class" caption="SuperWise UI showing a building floorplan, statistics, and other options (note image is taken from the Swegon website, not an actual running system)">}}

With the ability to supress/raise alarms, or perhaps inject malicious code via the UI to a system in the building) an attack could cause all sorts of issues, not least being the possibility for causing panic within the building. Alternatively, with the ability to schedule events, an attack could for example set up a schedule which makes all systems run att 100% overnight, generating an expensive power bill, and increased wear on systems. 
Naturally, the UI also allows users to update the BMS firmware by uploading a zip folder with the update code!

In regards to Tridium devices, they're used for a massive range of different purposes, so the impact of their default passwords being known depends truly on the target.   

## Final Thoughts

One of the main questions I've been asking myself for a while is - why aren't more (if not all) of these systems causing huge security issues which pop-up into the news. I think the answer is partly due to the fact that it's difficult (in particular with Tridium devices) to identify and search for precise targets, with many such controllers being exposed via IP addresses which simply lead back to internet service providers with ICANN searches. It makes it both challenging for white-hat hackers to report such systems, and for black-hats to identify systems of interest.

I also think it's important to note that attackers may, instead of using the access to tamper with buildings and gain information, use the devices in bot-nets, or as proxies, which represents (arguably) a greater collective issue than a few rogue sprinklers...
