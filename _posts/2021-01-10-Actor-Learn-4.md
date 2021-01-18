---
title: LabVIEW AF_4 User Interface Basics with Actor Core Override
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

The help loop is running parellelly with Actor Core.vi as we need to make sure QDSM is runing at the same time.

Recap the " **Actor Core**"
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/2.jpg"> </p>

Run the loop externally to send Message
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/5.jpg"> </p>

The loop can also be add into the over-rided "Actor Core.vi"

The UI Loop is also called "Helper Loop"

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/6.jpg"> </p>

## Create Simple Version of LabVIEW Core
[source](https://zhuanlan.zhihu.com/p/148606242)

1. Start with Queue Driven State Machine
To interact with user input, we need 2nd loop for event loop

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/Core_DQSM.PNG"> </p>


2. QDSM with OOP

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/Core_DQSM_OOP.PNG"> </p>

* Application Data is replaced, these data will determine Actor Name and data later
    * Complex.lvclass
    * Standard.lvclass

* Queue Element (Cluster in QDSM) is Message Class and its Instance
   * Message = Command + Data
* Each State Case -> Each Child of Message.lvclass Class
  * The Top "Do.vi" is a dynamic dispatch VI
  * Each Message Child Class has a "Do.vi" child class to override the parent "Do.iv"
* The input for "Do.vi" are
  * Actor Class, the actor data will be operated by "Do.vi"
  * Message Class (Queue Element), this determine which child "Do.vi" to use during rumetime

  3. Start changing to Actor
* In previous QDSM, two loops can be treated as two Actor
  * Top loop (actor) in charge of receiving message and handle message
  * Bottom loop (actor) in charge of collecting user actions and send out message
* The root actor launch queue = Message Enqueue
* Message Enqueue can be passed to other actor so other actors can send message to this one
* Message Priority Queue Object = Message Enqueue Object but
  * Message Enqueue expose to external (other) actors (Only enqueue is allowed)
  * Message Priority Queue expose to internal, that able to enqueue, dequeue, release queue
* The relationship between method and its message is
  * "Do.vi" will call its method
