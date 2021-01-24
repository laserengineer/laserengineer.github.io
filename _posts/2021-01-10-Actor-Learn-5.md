---
title: LabVIEW AF_5 User Event in Actor Framework
date: 2021-01-18
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

[Tom McQuillan Youtue Channel](https://www.youtube.com/watch?v=2k3ZDwJolbA&list=PLmF-6jvwRvVNFzBjzh4bQDjFbv6lShcth), [LabVIEW Sample Code](https://github.com/laserengineer/LabVIEW-Study.git)

## User Events in Actor Framework (Chat Window Project)

This video includes:
* Pre-Launch Init
* Actor Core overrides
* Stop Core overrides
* Use both overrides to create, generate and destroy user events.
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/5/Server UI.png"> </p>


### Function overview
>* Chat Room Server
  * Launch Chat Windows -> Launch multiple User (Actor) for chatting
* Send a message to every User (Actor)
* Send individual chat to specific user
* Detect how many users are online and automatically update if a chat window is deleted
* Individual User could choose to update various status
* Close all actors in a graceful manner
>

### Design Steps
#### Start and Stop Actors
It is the actor's responsibility to stop the help loop as opposed to the other way around to let other actors to stop this actor.

More details to the Actor
1. When we launch an actor, we actually launching actor.vi
2. Inside Actor.vi we have:
  * Pre Launch Init.vi -> Create Reference (Override)
  * Actor Core.vi -> Use the Reference
  * Stop Core.vi -> Release Reference (Override)

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/5/Pre-launch and Stop Core.png"> </p>

Start working on Overrides

<p align="left"> <img src="/assets/images/LabVIEW Actor Framework/5/Server Overrides.png"> </p>

Pre-launch vi
<p align="left"> <img src="/assets/images/LabVIEW Actor Framework/5/Pre-launch.png"> </p>

Stop Core.vi
<p align="left"> <img src="/assets/images/LabVIEW Actor Framework/5/Stop Core.png"> </p>

Server UI Block Diagram

<p align="left"> <img src="/assets/images/LabVIEW Actor Framework/5/Server UI_Block Diagram.png"> </p>

After close the panel:
* Actor is closed
* Windows is closed
* No locked items in project
* Nothing is accidentally running into background

### Summarize
* Pre Launch Init.vi -> Create **User Event**
* Actor Core.vi -> Dynamically register and use **User Event**
* Stop Core.vi -> Generate **User Event** and close event reference

DO NOT start any actor in Pre Launch Init.vi, the reason is for uplevel calling this VI- Launch Actro Core.vi is "Non-reentrant". It needs to wait the return of the new actor Enqueue (queue of queues). While Pre Launch Ini.vi is **Before** return Enqueuer
>Actor.vi sends the enqueuer back to launch actor core via the queue of queues, where launch actor core is sitting waiting for it. Hence a non re-entrant vi holds up any new actors from being launched.

We can also use user event to update front panel indicators as well.
Next chapter is for how to launch nested actors and keep a track of nested actors in the application.
