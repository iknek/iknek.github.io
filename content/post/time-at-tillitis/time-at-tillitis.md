+++
author = "Imad Alihodzic"
title = "Reflecting on (my Time at) Tillitis"
date = "2023-09-02"
description = "An overview of Tillitis, the TKey, and my time spent working there"
tags = [
    "authentication",
    "security",
]
categories = [
    "authentication",
    "security",
]
url = "/blog/time-at-tillitis"
aliases = ["security"]
+++

This summer, I had the opportunity to join Tillitis as a summer intern, handling their order logistics, in addition to working on device and client apps. In this post, I’ll introduce the TKey, and reflect on my time at Tillitis.
<!--more-->

## You may be wondering, what is Tillitis?

Tillitis is a Swedish company, which started as a part of Mullvad (the VPN provider), and grew to be its own spin-off company. They’re still a relatively small startup, working on a new type of security key, called the TKey. 

## What is the TKey?

{{< img src="/post/time-at-tillitis/key.jpg" alt="Picture of the Tkey" class="horizontal-class" caption="Picture of the Tkey. Credit: Tillitis">}}

The TKey is not a traditional USB which stores information. On the contrary, it’s a small FPGA computer, sporting a 32 bit RISC-V CPU. This CPU is complemented by a number of things, including a touch sensor, 128 kiB of RAM for applications, a true random number generator, and a status LED controlled by the CPU. For communication, a serial to USB CDC adapter is built-in.

Currently, there are two variants of the TKey - that being the regular (locked version), and the unlocked. ~~As TKey unlocked is not up for sale yet, though it will be very shortly, I’ll explain the regular, locked, variant.~~ Edit: The unlocked version is now up for sale! Find it [here](https://shop.tillitis.se/).

Before being sent to a customer, each (locked) TKey goes through a provisioning process, during which a bitstream is created and flashed to the device. This is essentially the FPGA configuration, and it includes the firmware in the ROM (not accessible to users), as well as the:
- Unique Device Identity (UDI)
    - 64 bit number, which includes information about the device, such as the serial number and version. Never changed during a TKey’s lifespan.
- Unique Device Secret (UDS) 
    - An integral part to the device’s security. Namely, it's the device’s unique, and completely random, secret, from which it can use to (for example) derive a public/private key-set. 

## How does the TKey work?

From a user-perspective, using a TKey is as simple as plugging it in, and then running an application on your computer. Behind this user interface, however, the process is a bit more complex.

### Ok so, how does it actually work?

To use a TKey, one essentially needs two applications. The first is called the client app, and it runs on a computer, phone, or any other device which the TKey can be plugged into. The client app is responsible for communicating and sending the second (device) app to the TKey.

The device app, which runs on the TKey itself, is responsible for the actual program logic. Once the device app is sent to the TKey, the first security feature, measured boot, is commenced. 

Namely, the TKey measures the loaded device application before starting it, creating a hash digest measurement with BLAKE2s. This measurement is combined with the aforementioned UDS, to create a base secret called the Compound Device Identifier (CDI). Users also have the option to input a User Supplied Secret (USS) into the CDI, for additional security. The algorithm for the CDI is subsequently: 

``` golang
cdi = blake2s(UDS, blake2s(device_app), USS)
```

## What does this process achieve?

To understand this question, one must again consider the fact that the TKey stores no application or user data on the device. So how do we make sure an application, such as a public/private key generator, can create the same key-pair over and over again, between device resets? 

The answer is the above explained measured boot. Namely, if the exact same TKey device app is loaded to a TKey, and the exact same TKey is used (keeping in mind the UDS never changes), and the exact same USS is input, the resulting CDI will always be the same. And, as keys are derived from the CDI, the same CDI will always generate the same key(s).

## Why do all this?

One of the main reasons is that the TKey simplifies, and strengthens, authentication. Unlike traditional authentication against a service (or why not even a service against a user?), the process is based on something the user has, the TKey, and something the user knows, that being the USS. One can even combine this with a requirement that the user touches the sensor on the device, adding an additional layer. All of this, with just a USB that plugs into a device.

But this is just the tip of the iceberg. Since the device uses measured boot, an end user can be sure that an app has not been tampered with, as any changes to the code (or e.g. compiler or packet import versions) will create an entirely different private/public key pair. 

## The other good stuff

One of the main features that sets the TKey apart from contenders is that both the firmware, and hardware, are completely open source. This brings with it the advantage that one can review the entire code and hardware design, to fully understand what the TKey does, and how it does it.

Another great benefit to the TKey is its versatility. Using the exact same open source libraries, and code as Tillitis, users can create new apps to extend TKey functionality. This could for example be an [encrypted file-storage vault using a TKey](https://github.com/0xMihir/Cryptum), or a TOTP app. 

## My Time Spent Working at Tillitis 

Honestly, it was a great experience. For starters, everyone who works there is great, and they were especially patient with me and all my questions! They have a very smart and skilled team which I think can take the TKey very far. 

Speaking of the TKey itself, I think it’s a really unique, and valuable, security key which has the potential to change how we handle authentication. There’s really no other contender who’s developing a key like Tillitis, in terms of functionality and security features.

Personally speaking, the uniqueness of the TKey made it a challenge to fully understand at first, though that may just be me. However, this presented a fantastic opportunity for me, as I’ve learnt an incredible amount about not just the TKey, but also security from an implementation perspective.

Another point really worth highlighting, in my opinion, is the fantastic company ethos. Every single person at Tillitis cares about security, and is passionate about making a truly great and open product, which provides a foundation for users to do as they wish. It’s a direct carry-over from Mullvad, who have an equally great ethos (and who have recently [campaigned against a proposed EU Chat Control 2](https://mullvad.net/en/chatcontrol)).

{{< img src="/post/time-at-tillitis/tkey2.png" alt="Picture of TKey on keychain with Tillitis strap" class="horizontal-class" caption="My own trusty TKey">}}

## Closing Notes

Once again, I’d like to thank everyone there for a wonderful, and very educational, time. 
The TKey shop can be found [here](https:/shop.tillitis.se), the general website [here](https:/tillitis.se), and the dev-book [here](https:/dev.tillitis.se).