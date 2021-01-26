---
title: LabVIEW AF_9 Abstract Message in LabVIEW Actor Framework
date: 2021-01-24
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

## Sending Messages to the Calling Actor User Interfaces by Using Abstract Messages

<span style="color:yellow">
<span style="font-size: 150%">
Notes: </span>
</span>

>The "Interface Message" is introduced into LabVIEW 2020. With multiple inheritance, we no longer need to create an Abstract Message. This post is simply to keep the whole learning progress complete and help to understand the Actor framework more completely.

Both Interface Message and Abstract Message approaches will be used. This article contains three methods of sending message from **Nest Actor** to **Calling Actor** will be introduced.
* Caller Enqueuer (Not recommanded)
* Abstract Message
* Interface

### Caller Enqueuer (Not recommanded)

Sometimes we need to send message from __Nested Actor__ to __Calling Actor__. And we DO NOT hope nested actor dependent on its calling actor. As before we alway hope to get Low coupling often correlates with high cohesion

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Caller Enqueuer/Nested Actor.png"> </p>

For a simple sample code, the message direction as showing above.
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Caller Enqueuer/Message Direction.png"> </p>

The most straight forward idea is to create an Update UI method in Calling Actor -> Create Message -> Send Message in Nested Actor
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Caller Enqueuer/Use Caller Enqueuer.png"> </p>

As we directly use Calling Actor Lvlib function "Send Update UI", we created a dependency between Nested Actor over Calling Actor. This will stop the Nested Actor from being reused in other applications without the calling actor being present.

For example, when we copy the Nested Actor in a new Project, then Calling Actor.lvlib showed in the dependencies.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Caller Enqueuer/Dependency.png"> </p>

So the Nested Actor is dependent on its Calling Actor, which we do not want. It is unable to reuse the Nest Actor due to coupling.

"Send Update UI.vi" message tie two actors together.
