---
title: LabVIEW AF_7 How Actor System Stop and What Happens When an Error Occurs
date: 2021-01-20
categories:
  - Study
tags:
  - LabVIEW
  - Actor
---
<span style="color:red">_First rule of coding - Just write the code._</span>

[Source](https://www.youtube.com/watch?v=2k3ZDwJolbA&list=PLmF-6jvwRvVNFzBjzh4bQDjFbv6lShcth)

[LabVIEW Sample Code](https://github.com/laserengineer/LabVIEW-Study.git)

## How Actor System Stop and What Happens When an Error Occurs

### Stop Nested Actor  

Inside Launch Nested Actor, enable "Auto-Stop" will record nested actor to manage the  Enqueuer reference

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Auto-stop.jpg"> </p>

When the actor stop, via "Stop Core" for loop to send stop message to each actor.
Send emergency stop for error code.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Nested Stop.jpg"> </p>

Top actor stop will send stop message to nested actors.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Stop Tree.jpg"> </p>

Only closed actor and nested actors are affected, other actor can still perform work.
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Stop Tree2.jpg"> </p>

### Error handle

In command message handle Do.vi, the generated error will trigger error handle case structure and then call Handle Error.vi method.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Actor Core.jpg"> </p>

Stop.vi method
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Actor Stop.jpg"> </p>

Stop method call Stop Core.vi, it is inheritance and dynamically dispatched VI.
It send out final error code to "send stop"
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Nested Stop.jpg"> </p>

The error pass out Actor Core.vi and use Send Last ACK.vi to wrap Error to its callers.
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Last Ack.jpg"> </p>

Hand Last ACK Core.vi read the error message and pass it to caller's QML

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Hand Last Ack.jpg"> </p>
Send the error from low to up level. The up level is responsible for close the nested actor.

The developer could override the following functions to change the behaviour.
_ Handle Error.vi
_ Last Ack Core.vi
_Stop Core.vi

Single Actor Start, run and close procedure.
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/7/Single Actor Stop.jpg"> </p>

1. Caller invokes Launch Actor, with Actor as argument
2. Actor invokes Pre-Launch Init
3. Launch Actor returns actor's enqueuer to caller
4. Actor invokes Actor Core, which runs until stop conditions (error or instruction) are met
5. Actor invokes Stop Core
6. Actor sends Last ACK, containing error information to caller
7. Actor Stops 
