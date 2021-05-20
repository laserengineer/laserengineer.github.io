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
