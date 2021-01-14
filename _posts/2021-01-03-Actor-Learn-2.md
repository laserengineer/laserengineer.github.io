---
title: LabVIEW AF_2 Actor Core
categories:
  - Study
tags:
  - LabVIEW
  - Actor
---
Actor Core
[Source](https://www.youtube.com/watch?v=2k3ZDwJolbA&list=PLmF-6jvwRvVNFzBjzh4bQDjFbv6lShcth)

[Source Code](https://github.com/laserengineer/LabVIEW-Study.git)

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

Actor Framework or Libraries have been implemented to permit actor-style programming in languages that don't have actors built-in. The actor framework works is essentially to launch asynchronous processes when required, use "Start Asynchronous Call Node"

Notes:
* All  the calculation/process is finished within specific Actor
* The only communicating method between different actors is through messages
* To respond messages, the actor could
    * Change actor state
    * Change actor behavior
    * Send messages to other Actors
    * Create other limited Actor

### Actor.lv Class
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/2/Override for Actor Core.png"> </p>

Actor LabVIEW Class has 7 Vi for override

Notes:

* The QDSM Shift registor determine the actor name
* Message Class is the Queue Element
  * Different Queue Element refer to different Message Class
* Top Message Class the "Abstract" Message
* Each message requires a new child class to override Parent Abstract Message
* Each child message class has a "Do.vi" to override the Parent "do.vi"
* "Do.vi" input are Message class (Queue Element ) Actor Class and
    * The message class determine which child class to use during the runtime
    * Actor Class input and output for Actor Data operations (Toggle)  



### Actor Core

Actor core.vi, Actors receive messages (to their inbox) and carryout tasks based on those messages. (Like a Queued Message Handler).

Actor Brain = Message Handling Loop (MHL)

Asynchronous call node to launch QDSM as Actor.

Compared with QDSM， use dynamic dispatch technology

* Shift Registor -> LabVIEW Class Data
* Message Class Data contains:
  * "Case Command"
  * "Data" used for each case
* **Do.vi** includes
  * Unbundle the cluster that includes commands and data
  * Case structure for each case
* Case structure -> LabVIEW Class, case message process is replaced by dynamic dispatch of Do.vi，
Use dynamic dispatch to replace case selector
* Case "State" (Command) is replaced **message Class**
* Data is wrapped into **Message private data** to get rid of variant


<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/2/framework Change 2.png"> </p>


<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/2/Class.png"> </p>

Pillars of OOP:
* Inheritance
* Encapsulation
* Polymorphism

In real application development, we need to abstract the Core.vi to Top ancestor's level

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/2/Top core.jpg"> </p>

This actor class the top ancestor's level and include the while loop within the Actor Core.vi method, set it as "Protect" to forbid external visit. Only child class is allowed to visit.

All the child class actors inherit from Actor.lvclass and overwrite the Actor Core.vi for different parellel tasks. Note:
* Child class Actor Core.vi need to keep invoke node for ancestor Actor Core.vi to ensure the message circulation

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/2/child actor core.jpg"> </p>

Elements of the Actor model

* Actor
    * An encapsulation of data, procedures and threads
* Message
    * Any information passing between actors
* Address
    * A label indicating message destination

Actor Principle
1. Actor can receive any message at any time
    * Actor must react to unexpected messages gracefully
2. Actor are self-deterministic
    * Message are always requests, never commands
    * Things are not done to an actor, an actor does things to itself

* Actors encapsulate data, procedures and threads
* Actors cannot predict the next message
* Actor Design != Actor System Design
* Addresses = Message Transport (Queue, User Event, etc.)
    * An address defines a location
* Each MHL is a new actor
* Each actor entails development overhead, all actors need exit conditions and error handling
