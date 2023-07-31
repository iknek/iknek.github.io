+++
author = "Imad Alihodzic"
title = "The Rise & Fall of Erlang at Ericsson AB"
date = "2019-07-31"
description = "The story behind the early development and downfall of the programming language Erlang"
tags = [
    "history",
    "programming",
    "erlang",
]
categories = [
    "stories",
]
url = "/blog/the-erlang-story"
series = ["Stories from the past"]
aliases = ["pentest"]
+++

This is the story of Erlang, a programming language that was maybe too good for its own sake.

<!--more-->

Corporate blunders are nothing new. Many of these blunders are widely known, such as the time Blockbuster declined to purchase Netflix, or the time Daimler-Benz lost 20 billion dollars on Chrysler. But how many remember when Ericsson created a programming language so disruptive, and so efficient, that they banned it? This is the fascinating and little known story behind Erlang.

## The Backdrop

The story begins in 1982, at the Swedish telecommunication giant Ericsson. Ericsson, at this time, decided to experiment with the programming of telecom - with the goal of improving the development of telephone equipment.
They began with a list of over 20 languages, including Lisp, Prolog, and Parlog, filtering out unsuitable ones with the help of 2 main criteria.

_Side note: I’m going to keep the explanations fairly simple here, so that it’s easier to understand for people who perhaps aren’t programmers._

The first criteria was that the language had to be “high level” and “symbolic”. A high level language is essentially a language that can be easily written and read by a person. This is then transformed by a ‘compiler’ into more specific instructions which a computer processor can understand. Symbolic means that the language uses characters or symbols to represent concepts like mathematical operations, for example plus (+) and minus (-). These two factors help make software development simpler and faster.

The second criteria was that the language had to have primitives for concurrency and error recovery, and the execution model couldn’t have back-tracking. Without going into the details, this essentially just means that the language had to be capable of:

1. Doing multiple things at once
2. Recovering and not crashing due to errors
3. Executing instructions in a certain order, without going back to previous instructions.

With the criteria defined, the team at Ericsson began narrowing down their list of potential languages. 3 years later, in 1985/'86, their list had gone from over 20 languages to… _Zero_. Whilst certain languages fulfilled some of their criteria, none of them had everything the team wanted. The solution was therefore to develop their own language, combining desirable features from all the evaluated ones.

And thus, Erlang was born!

## Early Development

Experiments with this new language began as soon as 1987. By 1989, they had reconstructed about 1/10th of the Ericsson MD-110 system using Erlang, as a proof of concept. And the results were staggering. Compared to the amount of time needed to develop the features with the then-standard at Ericsson, Erlang was faster by a factor of 3 to 25, depending upon the feature concerned! On average, productivity increased by a factor of 8!

{{< img src="/post/the-erlang-story/md-110.jpg" alt="Picture of the Md-110 PBX" class="horizontal-class" caption="The Ericsson MD-110. Credit: Järnvägsmuseet">}}

However, there were problems. This massive productivity increase factor was very controversial within Ericsson, and many theories were created to explain away the findings. Erlang’s late co-creator, Joe Armstrong, explained that:

> _“It seemed at the time that people disliked the idea that the effect could be due to having a better programming language, preferring to believe that it was due to some “smart programmer effect”. Eventually we downgraded the factor to a mere 3 because it sounded more credible than 8. The factor 3 was totally arbitrary, chosen to be sufficiently high to be impressive, and sufficiently low to be believable. In any case, it was significantly greater than one, no matter how you measured and no matter how you explained the facts away”._
> — <cite>Joe Armstrong[^1]</cite>

> [^1]: All quotes by Joe Armstrong come from his [article](https://dl.acm.org/doi/10.1145/1238844.1238850) during HOPL III, June 2007.

And even this paled in comparison to the bigger problem. Whilst development proved to be faster, Erlang itself was far too slow. For Erlang to be viable, it had to become 40 times faster.

Nonetheless, the team _(which by the way was just 2 people at this point!)_ knew they were onto something great. This was proven that same year at the SETSS conference in Bournemouth, where they had the chance to present Erlang to the world. Armstrong wrote in his paper that:

> _I remember Mike, Robert and I having great fun asking the same question over and over again: “what happens if it fails?” - the answer we got was almost always a variant on “our model assumes no failures". We seemed to be the only people in the world designing a system that could recover from software failures.”_ <cite>[^1]</cite>

They were in fact so confident that Armstrong goes on to say that

> _“At the Bournemouth conference everybody told us we were wrong […] but we left the conference feeling happy that the rest of the world was wrong and that we were right”._<cite>[^1]</cite>

Over the next 6 years, Erlang was improved and expanded greatly, speeding up the language significantly, and introducing many new features. When the team (which had by now grown to hundreds of people) wasn’t making vast technical improvements, they were busy marketing Erlang, and partnering up with companies. This included delivering their first copy of Erlang to Bellcore (now iconectiv) in 1990.

Then, in December of 1995, everything changed.

## The Big Break

In December of 1995, a massive project at Ellemtel called AXE-N failed. It's failiure was such that even Ericsson AB's website quotes their staff magazine saying: 
> _“People fainted at the meetings, some wept while others displayed no reaction at all; they appeared to be totally unmoved.”_ <cite>[^2]</cite>

> [^2]: Ericsson [article](https://www.ericsson.com/en/about-us/history/changing-the-world/big-bang/axe-n) about the AXE-N project.

The AXE-N, a switching system, was meant to be a hardware and software replacement for the older AXE10 systems, with the software developed in C++.

With this collapse, however, the door opened for Erlang, which was chosen to be the replacement programming language (whilst the hardware was re-used). The project recieved a new name, too, now dubbed the AXD. In his article for HOPL III, Joe Armstrong anknowledged that without this event, the language would have never left the Ericsson lab. 

Instead, this became the largest Erlang project (most probably ever), employing over 60 Erlang developers. Additionally, all external marketing for the language was stopped, with focus being solidly redirected to internal development. By the launch of the AXD 301 switch in March of 1998, the system had 1,13 million lines of Erlang code (this figure grew to over 2.6 million across the AXD's lifetime). 

{{< img src="/post/the-erlang-story/axd301.png" alt="Picture of an AXD 301 rack" class="horizontal-class" caption="An AXD301 rack. Credit: carritech.com">}}

But number of lines of code isn't, nor should it be, impressive part. What truly showed the potential of Erlang was the fact that out of the 60-person team of programmers, the majority came from industrial backgrounds, with no prior knowledge of concurrent or functional programming! Instead, most of them were taught Erlang by Armstrong and his colleagues. 

Furthermore, Armstrong claimed that by conservative estimates, one line of Erlang corresponded to about five lines of C, meaning that a corresponding system in C would have had over _6 million_ lines of code at launch. 

This is made further impressive by the fact that the AXD 301 had an observed nine-nines reliability. This meant that over a 20 year period that the AXD 301 provided services, the services were online 99.9999999% of the time.

Coupled with an estimated 4x increase in productivity during the development cycle, this meant that the project was by all measures wildly successful.

## Downfall

The Erlang team, however, didn't have much of a chance to celebrate. Even before the launch of the AXD 301 system, in February of 1998, Ericsson Radio Systems (a subset of Ericsson) had banned the use of Erlang within its company, claiming amongst other things that the use of a proprietary language wasn't beneficial. Many view this and other excuses as rather 'flimsy', which has given rise to a number of less-dicussed theories on the _actual_ reason for the ban...

Regardless, this effectively marked the end of Erlang within Ericsson as a whole. The team thus lobbied managment to release the language under an open-source license (with much of the lobbying work being done by Jane Walerud). Eventually, managment agreed, and Erlang was released into the wild in December of 1998.

## Aftermath

With Erlang free-to-use, the majority of the original development team (understandably) resigned from the company, starting Bluetail AB, with Jane Walerud as the CEO. Just two years later, Bluetail was sold to Nortel Networks for $152 million dollars. This triumphancy didn't last for longer, either, as the IT crash of the early 2000's came just half a year after the sale, leaving half the Bluetail team (un)employed at Nortel. 

## Closing Notes

If you found this interesting, I'd highly encourage you to read Joe Armstrong entire artical. It's a very interesting and far more detailed (and technical) reaccounting of Erlang's development. Most of the information in this post comes from there, and a lecture from [John Hughes](https://en.wikipedia.org/wiki/John_Hughes_(computer_scientist)) which I had the privelage of attending at Chalmers.