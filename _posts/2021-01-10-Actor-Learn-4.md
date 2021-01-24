---
title: LabVIEW AF_4 User Interface Basics with Actor Core Override
categories:
  - Study
  - LabVIEW
tags:
  - LabVIEW
  - Actor
---
<span style="color:red">
<span style="font-size: 150%">
First rule of coding - Just write the code. </span>
</span>

[Source](https://www.youtube.com/watch?v=2k3ZDwJolbA&list=PLmF-6jvwRvVNFzBjzh4bQDjFbv6lShcth), [LabVIEW Sample Code](https://github.com/laserengineer/LabVIEW-Study.git)

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

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/Core_DQSM.png"> </p>


2. QDSM with OOP

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/4/Core_DQSM_OOP.png"> </p>

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

## UI
>The Actor Framework does not provide the method VIs of your actor with any directly access to controls, indicator, or parallel loops that might be part of its override of Actor Core.vi. You must define that access within your actor.
>
> Here are two easy ways to define that access within your actor:
> 1. Include a reference in your actor's private data to either your Actor Core.vi or to tis front panel Object. Bundle these reference into the actor before invoking the Call Parent node. Your Actor can then access these references from within any of its methods. This method allows your methods to control the user interface of the Actor Core.vi. Although it may not be the most efficient approach. This approach works well for interfaces that are only displaying information and not reacting to users' actions.
> 2. Create a set of user events to manage your front panel update. Create these evens in your Actor Core.vi and bundle them into the actor object prior to invoking the Call Parent node. Then create a loop in your Actor Core that runs in parallel to call Parent node which is dynamically registered for the events. Whe n then actor receives a message, the handling function for that message can generate the appropriate event and pass along any relevant data.

> Alternatively, your may choose to use queues, notifiers or other mechanism to communicate with your parallel loop rather than events. Regardless of which mechanism your prefer, NI recommended that you select only one data transfer mechanism within any given actor and keep the total number of sunch mechanisums to a minimum within the application as a whole. Be sure to provide a mechanism to stop your parallel and trigger that mechanism by overriding *Stop Core.vi* in your actor Child Class
>
