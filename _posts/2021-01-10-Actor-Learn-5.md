---
title: LabVIEW AF_5 User Event in Actor Framework
date: 2021-01-19
categories:
  - Study
tags:
  - LabVIEW
  - Actor
---
<span style="color:red">_First rule of coding - Just write the code._</span>

[Source](https://www.youtube.com/watch?v=2k3ZDwJolbA&list=PLmF-6jvwRvVNFzBjzh4bQDjFbv6lShcth)

[LabVIEW Sample Code](https://github.com/laserengineer/LabVIEW-Study.git)

## User Events in Actor Framework (Chat Window Project)

This video includes:
* Pre-Launch Init
* Actor Core overrides
* Stop Core overrides
* Use both overrides to create, generate and destroy user events.

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
#### 1. Start and Stop Actors
It is the actor's responsibility to stop the help loop as opposed to the other way around to let other actors to stop this actor.

More details to the Actor
1. When we launch an actor, we actually launching actor.vi
2. Inside Actor.vi we have:
  * Pre Launch Init.vi -> Create Reference (Override)
  * Actor Core.vi -> Use the Reference
  * Stop Core.vi -> Release Reference (Override)

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/5/Pre-launch and Stop Core.PNG"> </p>
