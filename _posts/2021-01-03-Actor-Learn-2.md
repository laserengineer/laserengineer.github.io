---
title: LabVIEW AF_2 Actor Core
categories:
  - Study
tags:
  - LabVIEW
  - Actor
---
## Deriving the Actor Core in Actor Framework (The Command Pattern)

### QDSM and QDMH Structure

Queue-Driven State Machine is  comparable to Queue Driven Mesage handler
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/2/framework Change.png"> </p>

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/2/2.jpg"> </p>

Issues:

* Poor modularization
* Poor Reuse
* No support for asynchrony VI calling


### Actor model and Actor Framework
[Wiki] Actor model is a mathematical model of concurrent computation that treats actor as the universal primitive of concurrent computation. The actor could
* Make local decisions
* Create more actors
* Send more messages
* Determine how to respond to the next message received  

[NI] Actor Framework is a software library that supports writing of application in which multiple VIs run independently while communicating with each other.

Each VI represents some actor carrying out an independent task in the system.  

Actor Framework or Libraries have been implemented to permit actor-style programming in languages that don't have actors built-in.

Notes:
* All  the calculation/process is finished within specific Actor
* The only communicating method between different actors is through messages
* To respond messages, the actor could
    * Change actor state
    * Change actor behavior
    * Send messages to other Actors
    * Create other limited Actor



### Actor Core

Actor core.vi, Actors receive messages (to their inbox) and carryout tasks based on those messages. (Like a Queued Message Handler).

Asynchronous call node to launch QDSM as Actor.

Compared with QDSM

* Shift registor -> LabVIEW Class
* Case structure -> LabVIEW Class
* Use dynamic dispatch to replace case selector
* Case "State" (Command) is replaced **message Class**
* Data is wrapped into Message private cluster to get rid of variant

Pillars of OOP:
* Inheritance
* Encapsulation
* Polymorphism
