---
title: 2021-07-14-Actor-Model-Learn-13
date: 2021-09-02
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

Every message that we send an actor will inherit from message class. When the receiving actor handles the message (it happens in Actor Core.vi) it will call the **Do.vi** method of the Message Class. So new messages just need to override this Do.vi to perform the target actions.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Do-Method1.png"> </p>

That being said, the messages class are **NOT** related to the actor class (it's a Association or "Uses" OO relationship).

So what can messages do to the actor objects? They can only call public methods which means that normally all a message class is doing is calling one public method of actor. The act of creating a message class and writing the "Do" method is pretty well scripted so you almost never actually have to manually create this code. The now workflow is to create the actor method then run the scripting code that create a message class for that method.

# Part 3 - Launching and Communicating

## Launching Actors

Launching an actor is the process of starting up the message handling loop of the actor. When an actor is launched a new thread is created (or a VI run asynchronously in LabVIEW Speak) and the message queue references are created for access the queue. An important concept that when working with actors is that actors can launch more actors. The first actor in the chain is known as **Root Level Actor**. A root level actor may launch another actor known as Nested Actor. A nested actor can launch more nested actors.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Launch-Actor-Method1.png"> </p>
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Launch-Nested-Actor.png"> </p>

Notice how all threads are created as need. The means that we don't have to decide at design time how many or what kind of actors to launch. Your program can wait until the user clicks a button or a DAQ value is read or any other condition is met. Then launch an appropriate actor to handle it. This is extremely powerful when creating highly dynamic applications. When you launch an actor you will get a reference to it's message Enqueuer. This is what you will use to send message to your actor.

## Messaging Actors

When an actor has been launched, we could send message to this launched actor by using its Enqueuer. By using the Enqueue method of the Message Enqueuer class. If people using the scripting tools methods to help you send messages will be scripted for you. These will make sure all your messages contain the correct data and the message class is correctly constructed. Actors can easily send themselves messages. They just a reference to their own message queue. They can get that using the "Read Self Enqueuer" Actor Message.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Read-Self-Method1.png"> </p>

Once you've done with your actor you need to tell it to stop. Built into the framework are the stop and Emergency stop message, either of these will cause the message handler loop to stop.

## Actor Messaging Actors

When an actor launches a nested actor, it will almost always also send message to it. It can do that easily using the message queue reference that it gets when the actor is launched. It is often useful for a nexted actor to send a message back up the chain. You can get your calling actor's message queue using the "Read Caller Enqueuer" Actor method.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Read-Caller-Method1.png"> </p>

Most of the time this is how the communication between actors works, the Root Actor is talking to its nested actors. Those nested actors talk to their nested actors, etc. You end up getting this sort of pyramid ship of actors running.

It is possible for actors to communicate directly with actors not above or below it in the pyramid, but you (as implementer of all the actors) are responsible for passing around the message enqueuer reference as needed to this happen.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Actor-Pyramid1.png"> </p>

Now we should begin to see the power of the framework. We can design each actor to be a small, well defined, easily testable piece of code. Then we can create another actor that launches our small actors, and another actor above that launches that actor...until we have a very complex system. The problem is you have a lot of loops running all messaging each. It is easy to get into a "wait, what's actually running right now" situation. Well people really tired of that.

# Part 4 - Being Productive with Actors

Now we know that Actors are really just object oriented Queued Message Handles, and we know how to start the message handling of actor and how to message that loop. Now we need to know how to actually get stuff down using actors.

## Actors are Objects, so let's extend them.
An actor is implemented as an LVClass. This means that if we want to extend it's functionality all we need to do is override some methods.

### "Classic" OO Extension

Message for our actors are just method calls on the actor object. We could make those methods dynamic dispatch if needed. Next we can create child versions of that actor that all implement the dynamic dispatch VI differently. This means we can send the same message to different actors and they will handle the message as they see fit.

For example, let's create a "Logger.lvclass" Actor. This actor can handle one message called "Log". This takes a string input with the string to be logged.

So if we crate a Dynamic Dispatch **Log.vi** for my "Logger" actor and create a message for that method. Now we can create a "CSV Logger.lvclass" that extended my "logger" Actor and create and override **"LOG.vi"** with the CSV functionality. That means when we launch a CSV Logger, anyone can send it a Log Messaged and it would be logged to a CSV format.

We can also create a "TXT Logger.lvclass" that extend our "Logger" Actor and it will handle the log message differently.

Things to remember, for a well-designed class means any method that can be called on the parent, we can call it on the child (the Liskov Substituion-Principle). Actors are classes, so they follow the same rules.

### Helper loops

In general what we need is more than a QMH where a Producer Consumer type architecture is more practical and helpful. This can be achieved in AF by overriding the framework method called "Actor core". Actor core is marked as a "Must call to Parent" VI. This is because the parent is our message handling loop.

So if we override it, we can get **additional** loops running alongside our message handler.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Helper-Loop.png"> </p>
Notice, how the help loop is stopped when the parent actor core stops

Helper loops are what do the bulk of the work for your actor, it does most of the heavy lifting jobs.
 * Need a user interface for the actor-> Put the event handler in a helper loop
 * Need to read from a DAQ device -> Perform the read in the helper loop.
  * The helper loop will probably need to message actor core using the "Read Self Enqueuer" method.
  * The actor core may need to message helper loop as well
  * It is up to designer to decide what communication mechanism is appropriate for this.

Things to remember that, once we started the helper loop, we are responsible for stopping it. The framework takes care of stopping the message handler loop, which is inside the call parent actor core node, but we need to stop our helper loop(s) when it's time. If we fail to do this, we will end up with an actor running in the background that we can't communicate with. This is often the most frustrating part of working with actors, but once we make the mistake a few times we will remember to be on the lookout for it.

### Doing work in Messages vs in Helper loop

Our actor can only handle one message at a time. The actor core will dequeue these message in the order they came in (respecting the priority). Each message will call a method on the Actor object.

If we were to put code that takes a long time to execute in that message, we will delay the handling of the next message until the slow code is done. That means that any other message (even ones that are higher in priority) won't be handled promptly. The AF doesn't make any promises about timing, but in in general we would like to handle messages quickly.

To accomplish this, you we should put all of the long running code inside a helper loop. Our actor core will tell our helper loop what to do (via all of LabVIEW'S normal interprocess communication tools) and then it will go back to handling messages. Our helper loop will then take the time to actually perform the task. This allows our message handler to be nice and prompt while still allowing our actor to do a lot of work.

For example: We have a file that needs to be processed. It is a pretty big file that takes several seconds to open, read and analyzed. If we just executed our "Analyze" code in the AF messages, then there would absolutely no way to talk to the actor until the analyze code is done.  What if I needed to abort it? It would be impossible.

 So instead, we could create a helper loop that would perform the analysis. Now when our actor received the "Analyze" mothod it would just send the file to the helper loop, then go back to wait for messages. The helper loop would then start working through the file. No if our actor core received an "Abort" message it could again message up the help loop and tell it to stop. Somework will be build in some mechnisms to make these actions work, but it is possible and easier to debug.


### Relationship Between Helper and Message Handler Loops

One of the main goals for the Actor Framework is to create chunks of code that are loosely coupled (or ideally, not coupled at all) to the rest of our code. The more we can separate loops, the easier it will be to debug your code. This means that each actor will be one thing. It has a small, public interface that other pieces of code can call into.

Each actor should be loosely coupled to other actors. This is not, however, how your helper loops should be related to your message handler loop. The helper loop SHOULD be tightly coupled to your message handler loop. There should be references shared between the two loops, queues that are expected to be flushed at the right time or any other communication scheme we need. This tight coupling between the loops is what will allow us to get so much done so quickly.

We, as the designer of our actor, are responsible for making sure we don't do something dumb that cause errors. And it is a reasonable thing for us to do since the helper and message handler loops are parts of our actor.

### Other Over-ridable Methods

In addition to actor core, there are some other overridable methods. See the list below for information

#### Pre-launch Init

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Pre-Launch-Method1.png"> </p>

 * **Executed** while your actor is being launched. All of the communication references are valid in this VI.
 * **Override** when you need to initialize something for your actor, a lot of the time you will initialize references in this VI.
  * References created here will have the same lifetime as our actor
  * If we need to send ourselves a message before anyone else, send it in this VI
  * Wiring an error to "Error Out" will cause following actor not to launch. The code that tried to launch the actor will get this error from the "Error Out" terminal of the launch actor. Use this to let your caller know if couldn't launch. For example, if we failed to open a log file that our actor needs to work

* **DO NOT** launch a nested actor in pre-launch init. That will create a deadlock.
  * Launch nested actors in our actor core.  

#### Stop Core


<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Stop-Core-Method1.png"> </p>

* **Executed** when your message handler loop stops running
* **Override** when you need to perform some clean up action at the end of execution. This can also useful for stopping your helper loops

#### Handle Error

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Handle-Error-Method.png"> </p>

* **Executed** when a message returns an error. This method is responsible for determing when the actor's message handler loops stops
* **Override** when you want to implement your won error handling
  * By default, the actor will stop itself on all errors
  * Note: When a Stop message is received, you'll get an Error Code 43 in your handle error (a critical error will be 1608). A lot of times the error handling code will clear all errors. If you do this your actor will **never stop**. You need to clear all errors, but wire a true to the "Stop Actor" output if the error code is 43 or 1608

#### Handle Last Ack Core

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Last-Ack-Method1.png"> </p>

* **Executed** when a "Last Ack" message is received. Every time an actor stops execution it will send its status up to its caller as a "Last Ack"
* **Override** if you need to perform an action when a nested actor has stopped
* The last Ack message has the following information
    * The error information that stopped the actor
    * The Actor's final value
    * The actors caller-to actor enqueuer (this was the actor's self enqueuer)

#### Substitute Actor
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Substitute-Method1.png"> </p>

* **Never Executed** directly by the framework. You have to decide when to run this method
* It is possible to change the actor's type inside a message. This is useful when implementing the State Design Pattern
* When changing states you may need to copy over some private data to the new state.**Override** this method to copy over this private data.

#### Receive Message
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Receive-Method1.png"> </p>

* **Executed** every time a message is received by the actor but before the message is handled
* **Override** when you need to perform an action on the method before it is handled
* This is very rarely overridden, mostly only while debugging

#### Drop Message Core
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Message-Drop-Method1.png"> </p>

* Part of Message.lvclass
* **Executed** when a message is leftover on the message queue after an actor has stopped. This can happen when an actor is stopped before it is done handling all of the messages in its queue.
* **Override** when you need to perform an action if a message gets dropped. Most messages (messages containing all By-Value fields) do not need any special drop handling. Usually you will only need to handle this case when a message is passing off the lifetime of a By-Ref item
