---
title: LabVIEW AF_6 Actor System Design and Messaging
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

## Chat Window_Actor System Design and Messaging


### Actor Orientated Design

Actor Orientated Design (AOD), Root Actor (Calling Actor) and Nested Actors

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/6/Root Actor.png"> </p>

Communication is created by saving Enqueuer to Actor Class Private Data.
* Self-TO-Caller Enqueuer
* Send-TO-Self Enqueuer

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/6/Enqueuer Actor.jpg"> </p>


When a "Caller Actor" call "Nest Actor":
* Launch <X> Actor.vi
* Pass "Caller Message Queue" (Root Actor for example) to nested Actor
* Get Caller Actor's Message Queue Reference Queue
* Exchange Message Queue Class to setup two-way communication

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/6/Nexted Actor Message Queue.jpg"> </p>


To be clear that, message tree is **Different** with Inheritance class. There is not Parent and Child relationship, they all incidence of original actor (Actor.lvclass)


Message is exchanging via Message Queue. Every actor can only access two message queue :
* Self Message Queue (Callee Message Queue)
* Caller Message Queue

As shown below:
*  The callee Actor **Y** can access self message queue via route __4__, and access caller Actor **X** via route __3__

* The main caller **x** can access self message queue via route __1__, and access caller Actor **Y** via route __2__

<p align="Left"> <img src="/assets/images/LabVIEW Actor Framework/6/exchange message.jpg"> </p>

Summarize the communication rule for Root Actor and Alpha, Beta Actor
1. Root Actor can send message to Alpha, Beta and itself
2. Alpha Actor can only send message to Root and itself
3. Beta Actor can only send message to Root and itself
4. Alpha and beta can not send message to each other

<p align="Left"> <img src="/assets/images/LabVIEW Actor Framework/6/No Comm Nested Actor.png"> </p>



<p align="Left"> <img src="/assets/images/LabVIEW Actor Framework/6/DAQ Message Tree.jpg"> </p>


### Develop

The 'Model' will be the 'Brains' of the Chat Room Windows. The 'Controller' is the user interface. I.e. what the user 'Controls'

The output of launch nested actor is Nested Actor's Enqueuer. It returns the reference to the enqueuer. Use this reference to send messages to the newly launched actor. It is the child of Message Enqueuer.lvclass
<p align="Left"> <img src="/assets/images/LabVIEW Actor Framework/6/Message Queue Class.png"> </p>

Structure of the application
<p align="Left"> <img src="/assets/images/LabVIEW Actor Framework/6/Server Chat Room Model.png"> </p>

Message route  

<p align="Left"> <img src="/assets/images/LabVIEW Actor Framework/6/Message Route.png"> </p>
