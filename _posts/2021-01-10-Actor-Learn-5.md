---
title: LabVIEW AF_5 User Interface Basics with Actor Core Override
date: 2021-01-13
categories:
  - Study
tags:
  - LabVIEW
  - Actor
---

## User Interface Basics with Actor Core Override

[Source](https://www.youtube.com/watch?v=2k3ZDwJolbA&list=PLmF-6jvwRvVNFzBjzh4bQDjFbv6lShcth)

[LabVIEW Source Code](https://github.com/laserengineer/LabVIEW-Study.git)

### First rule of coding - Just write the code



In Actor Framework UI development, we need to inherit "Actor Core.vi" and extend its functions.

The UI Loop is also called "Helper Loop"
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/1.jpg"> </p>

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/Actor QSM.jpg"> </p>

Recap the " **Actor Core**"
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/2.jpg"> </p>

Run the loop externally to send Message
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/5.jpg"> </p>

The loop can also be add into the overrided "Actor Core.vi"

The UI Loop is also called "Helper Loop"

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/6.jpg"> </p>
