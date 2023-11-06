+++
author = "Imad Alihodzic"
title = "The Shodan Lantronix Dork"
date = "2023-11-06"
description = "A quick summary of a dork I haven't seen discussed previously, with an alarming high vulnerability discovery rate"
tags = [
    "pentesting",
    "security",
    "IoT",
]
categories = [
    "authentication",
    "pentesting",
    "security",
]
url = "/blog/shodan-lantronix-dork"
series = ["Pentests"]
aliases = ["pentest"]
+++

Recently I've found myself sitting on Shodan.io, trying out different things and generally exploring the tool. By chance, I ended up finding a Lantronix device, which is where things got interesting!

<!--more-->

## What are Lantronix Devices?

First of all, Lantronix is a company which provides a range of different IoT products, targeting "*high-growth applications in specific vertical markets, including Smart Grid, Intelligent Transportation, Smart Cities and AI Data Centers*". In simpler terms, they make devices like serial to ethernet adapters.  

Many such devices are configurable via a web interface, accessible via HTTP on a browser (typically on port 80), or via `telnet` on port 9999.

## A Closer Look

As afformentioned, I ended up coming across a Lantronix device in the wild. Before doing anything else, I checked if the organization who owned the device had a bug bounty set up, which they did! Safe in the knowledge that what I was doing was legal, I tried to see what was behind each exposed port for the IP address. One of those lead to a log-in page, for the Lantronix device. With no valid combination of "admin" and "pass" or "password", I resorted to doing a simple web search for default Lantronix device passwords.

## The Exploit
It's at this point I found an interesting note in a user manual. The default password is, in-fact, the last 8 characters of the Device ID! Going back to Shodan, we find none other than the Device ID printed in the console history on port 9999. 

{{< img src="/post/shodan-lantronix-dork/lant3.png" alt="Image of the port 9999 response in Shodan (sensitive information redacted)." class="horizontal-class" caption="Image of the port 9999 response in Shodan (sensitive information redacted)">}}

With that identified, the login portal can be revisited. Leaving the username blank, and setting the last 8 Device ID characters into the password, we're granted access!  

{{< img src="/post/shodan-lantronix-dork/lant2.png" alt="Image of Lantronix web portal." class="horizontal-class" caption="The Lantronix Web Portal (sensitive information redacted)">}}

To conclude, a dork was promised and a dork shall be recieved: 

    "Lantronix" "Device ID: " port:9999 
 

## Final Thoughts

I think that its important to note, despite the extreme ease of the attack and the relatively low impact one miss-configured IoT device, that this is just one of easily hundreds of exposed IoT devices, that anyone could find and access in just a few minutes. 

With the solution (changing the default password or even just hiding the Device ID) being so simple, there is no reason for the vulnerability to exist in the first place. 