---
title: 2021-07-14-Actor-Model-Learn-13
date: 2021-07-14
categories:
  - Study
  - LabVIEW
tags:
  - LabVIEW
  - Actor
---

[Learn notes for Actor Framework Basic by Moore Good Ideas](https://www.mooregoodideas.com/actor-framework/basics/AF-basics-part-1/)

Learning notes for Actor Model again.
It has been quite a long journey curve for me to learn LabVIEW Actor Framework. It takes a lot of courage to use this framework in a real time challenging project.

That has being well said
“If you cannot do great things, do small things in a great way.”

So let us go back to the beginning of "Queued Message Handler" (QMH)

# Part 1 - The Basics  
## QMH
QMH design pattern has the following parts
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Classic-QMH.png"> </p>

- [x] The **Data**, it is usually the type defined cluster on the left shift register. Everything about the internal state of QMH is stored here
- [x] The **Queue**, this is how you get data into the QMH and tell QMH what task to do
- [x] The **Message Handler**, This is the "thread" that QMH executes on. This keeps running until QMH decides that it's done. This is a while loop with case structure inside.
- [x] The **The Messages**, each message has an identifier (the string e.g. "Start", in the image) and data (variant)

### Pros for QMH

1. Data Encapsulation: Only this specific QMH has the capability to handle specific functions, no other parts of the code can come in and put QMH in an invalid state

2. Separate Thread Execution, multi-threaded application, if we need a new thread to execute something, we can just implement it as a QMH, then send it messages and get the task finished

### Cons for QMH

1. **The open Queue**: Passing a basic LabVIEW Queue Reference around is a not smart move. It could happens that something else will destroys the Queue. Or something else decides its message has higher priority to enqueue it at the beginning instead of end, or something even worse-> flushes the queue first.
2. **Loosely Typed Message**: Almost all QMH's use a message ID field (usually an enum or a string) and a Data field (usually a variant).The first thing for QMH does in its message handling code is covert the Data variant into a useful/more clear type. This means that the code sending you a message has to know something about what's target task. It has to get the variant type correct. If the message data is changed, you need to make sure you remember to go and update any code sending you QMH a message.
3. **Creating New Threads isn't Handled by the Framework**. If you want 1 QMH, you drop the code in 1 spot. If you want 2 of the same QMHs. You drop the code in two spots. If you want 3... It would require a decent amount of VI Server WORK which is finicky to get the right.
4 **Extendable** What if you want a new QMH that was exactly like the other QMH only it handled a new message. Maybe you wanted 2 QMHs that were exactly the same, except the way they handled this one message.


## Solution for QMH Cons

By using Object Oriented approach, we could address above problems
1. **Encapsulate the Queue**: Instead of passing the queue around, we'll pass a class around. This class will allow other piece of code message the QMH. The guts of how this works will be **abstracted** from everyone, but most importantly , we'll be very careful about what we allow people to do with queue. It'll be impossible to flush the queue or do any of that other bad stuff. They'll only be able to enqueue at the correct end.

2. **Strictly Type the Message**: Create a message class and every message will inherit from this class. Each message class will have its own private data and constructor and such which will eliminated the variant. Every message will always have right kind of data with it. If we change the data associated with message, everyone who uses the message will automatically be broken until they update their code.

3. **Take Care of Creating New Threads**: This a little trickier but if we design our OO QMH we should be able to write the VIs and create the new threads *generically* so **EVERY** QMH we have will be launched in the same way. This means you only have to debug the code once and then we can forget about it.

4. **Make Each QMH an Extedable Class**, use the OO design patterns and tricks to make application easy to extend as needed

The **CONS** for OO QMH:
1. There are a lot of classes to create and a non-trivial amount of code for just the framework.
2. It takes a pretty long time to create and longer to debug.  
3. Confusing Classes: The Queue for QMH message delivery is a **DIFFERENT** classes than QMH class(store private data)
4. We will send the Queue Reference to use the methods "Send Perform Action" and "Perform Action"

# Part 2 - The Actor
The [*Actor Model*](https://en.wikipedia.org/wiki/Actor_model) is a Math/Computer Science concept.
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/af-icons.png"> </p>
Actor is a computational entity:
1) It contains state information
2) It can send, receive and handle message.
LabVIEW Actor Framework (AF) is just an implementation of the Actor Model in LabVIEW. It is an object oriented implementation developed and maintained by NI.

| QMH Component| AF Component   |  notes  |
| --------   | -----:  | :----:  |
| The Data     |Actor Class |State Information is stored in the Actor's private data  |
| The Queue|   AF Message Queue   |   The Message Classes wrap a priority Queue   |
| The Message Handler |    The Actor Core Method    |  Actor.lvclass: Actor Core.vi has the message handling loop inside it. New Message are added by overriding classes and methods|
| The Messages|   Message Classes  |   Message Classes all inherit from a common class. Each message has its own unique data associated with it|

This gets us all of the PROS from the QMH (state data encapsulation and separate thread execution) while implementing all of the stuff of OO QMH section.

What is the cons?
* There are a lot of classes. The project may not modularization
* Code is harder to read. It takes a while to figure out how to read a blokc diagram for an actor.  

## Actor Framework Classes

The AF does a lot in the background, the framework code to make everything happen is very complicated and confusing. So it is easy to see *WHAT it DOES* instead of *HOW it Works*.

### Actor.lvclass
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Actor-Icon1.png"> </p>
This is the main point of extension for the framework. Each QMH that we want to implement will be a child of the Actor Class, so the Actor.lvclass is the parent class for all the QMH. Notice that there aren't really many public methods. All of your extension and use will come form overriding the protected methods and adding methods to your child implementations.

### The Message Queue

 * This queue is a bit complicated for good reasons.
 The first thing need to know that it is a priority queue , when we message an actor you , three priorities: Low, Normal and High will be assigned.There is another "secret" priority of Critical but is only used by the framework.

 * This priority queue is wrapped in a few classes:
 ** Enqueuer
 ** Dequeuer, these are known as the queue pairs
 ** These pairs wrap the same queue reference deep down, but give us a way to encapsulate limited functionality of the queue.
** We could control someone's access for enqueue by providing enqueuer.
** We could also control some's access for dequeue by providing dequeuer.

** In general, you'll never see the dequeuer side of the queue (it's used inside the framework though), and you'll pass around the enqueuer side to everyone who needs it.  

### The Message Class

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Message-Icon1.png"> </p>
