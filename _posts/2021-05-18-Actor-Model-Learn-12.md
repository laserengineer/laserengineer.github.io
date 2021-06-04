---
title: 2021-05-18-Actor-Model-Learn-12
date: 2021-05-18
categories:
  - Study
  - LabVIEW
tags:
  - LabVIEW
  - Actor
---

[Hewitt, Meijer and Szyperski: The Actor Model (everything you wanted to know...)](https://www.youtube.com/watch?v=7erJ1DV_Tlo)

Learning notes for Actor Model
It has been quite a learning curve for me regarding LabVIEW Actor Framework, some details are not quite clear to me.
* Object oriented programing (OOP) thinking mindset needs to be improved
* The understanding for Actor Model needs to enhanced

So this is a learning note for Actor Model from Youtube video

## What is an Actor

* It is the fundamental unit of computation， it has to embody three essential elements of computation
** Processing -> Get something done
** Storage -> Able to remember & store things
** Communications

It is a primitive unit


## Actor system

One actor is no actor, they have to come in systems, then address is assigned to different parts of the system.
Actor can do three jobs:
1. Create more actors
2. Send message o actor it knows
3. Designated what to do with next message to itself

Conceptual: 1 message at 1 time, but the control part can send multiple messages at sametime

Message can only be delivered one or zero times in arbitrary order

Address != Identity

## Actor system Communications

Obviously my head is not designed for under Actor Framework, fail into sleep multiple times.
We can not simply combine LabVIEW Actor framework with actor model


In LabVIEW an Actor is a while loop that capble  to
* Create new actors
* Receive messages (dequeue)
** Make local decision (case structure)
** Perform actions
* Processes exactly one message at a time
* Send messages

Actor is always Asynchronous Call

## Actor Principle

### 1. Actors can receive any message at any time
* An actor has control over who has its address
* Each actor sends msgs on its own schedule
* Actors must reactor to unexpected messages gracefully

### 2. Actors are self- deterministic
* An actor is responsible for itself
* Messages are always requests, never commands
* Thins are not done to an actor; an actor does things to itslef

### 3. Actor use Hierarchical structure
* Promotes encapsulation
* Easier to reason about the behavior
* Better scalability

Actors encapsulate data, procedures and threads
Actors cannot predict the next message
Actor Design != Actor system Design

Actors encapsulate data, procedures, and threads
Actors cannot predict the next message
Actor Design != Actor System Design

## Actor Tips
### 1. Each MHL is a new actor
* Behaviors define actor, not artifactors (VI, Lvlib, etc.)
* MHL is the actor's public interface
* Usually maintains all actor state data

### 2. Each actor entails development overhead
* All actors need exit conditions and error handling, implement these features first

### 3. Not All Loops Are Actors
* Some processes are not conducive to MHL
* Delegated to "Helper Loops"

### 4. Actor works best when the MHL has to wait
* MSG processing time << Time between msg
* MHL should handle every message "instantly"
* Do not use it as a job queue
* Priority messages are unnecessary
* Use the *Helper Loop* as Job Processor
* The MHL is only for *Message handling*, the slow process/work should be handled by Helper Loop. MHL is the actor's brain (commander), helper loops and sub actors are the arms and legs doing the heavy lifting.
* Do not try to do much in your MHL, delegate to sub actors and helper Loops

## Actor Framework Features

* Uses inversion of Control to guarantee safe execution
* Priority Queues for inter - actor communication
** Actor's address is its queue refnum
* MHL provided by the framework
* Actors are launched dynamically
* Actors are multi-instanced
