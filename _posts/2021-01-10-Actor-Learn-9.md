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


### Abstract Message

"Abstract Message" is a method for Nested Actor and Calling Actor communication without dependency. (Before LabVIEW 2020)

In this case, Nested Actor only in charge of sending message and does not care who receive the message and how the receiver (the Calling Actor) perform upon the information. And Nested actor is not going to find out. Calling Actor only in charge of receiving and action, does not care who send the message.

To sum up, there are two parts in Abstract Message.
1. The Nested Actor sending the message
2. The Calling Actor receive the message

No dependency between Calling actor and Nested Actor, but there will be dependency between Nested Actor and Abstract Message.
1. Create Abstract Message in Nested Actor (what message to send)
2. Create Child Abstract Message in Calling Actor (what to do after message)
3. In Nested Actor, write the "Child Abstract Message" to Nested Actor calling method when button is clicked. (how to send the message)
4. Create a message for item 3 method (how to send the message)
5. Send item 4 message in the event loop (how to send the message)

#### Create Abstract Message _Nested Actor

In Nested Actor Library, Create Abstract Message for caller.
Right Click Nested Actor.lvclass -> Actor Framework -> Create Abstract Message for Caller

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Create AMC.png"> </p>

Enter a name for the New Message

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/New Abstract Message.png"> </p>

Then define the attributes to the new message, in this case, only a string control is needed (it could be replaced by Cluster for the future project). Then click "Create Message".

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/New Abstract Message1.png"> </p>

The Abstract Message "New Data Msg.lvcalss" for Calling Actor is automatically created
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/New Abstract Message2.png"> </p>

It is worthy mentioning that New Data Msg.lvclass is the **direct** child of LabVIEW Message.lvclass, although owned by Nested Actor.lvlib, it is a dependent class **NOT** owned by Nester Actor.class   

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/New Abstract Message3.png"> </p>

As mentioned before, Nestted Actor only care about sending message, so there is only "Send New Data.vi", **NO** "Do.vi" in this lvclass. So the nested actor created an "Abstracted Layer".

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/New Abstract Message4.png"> </p>

#### Create Abstract Message _Calling Actor

Nested Actor has created the sending message function and have no idea how the message will be handled, now it's Calling Actor turn to create a method that we want to execute when we receive a message from Nested Actor. In our case, we want to **Update UI.vi** to execute when a message saying a data is available.

Right click **Update UI.vi**-> Actor Framework -> Create Child of Abstract Message
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Child of AM.png"> </p>

Select the parent class -> the message class (New Data Msg.lvclass),  we created from Nested Class.
Then we have creatd the "Update UI Msg.lvclass" which is the child of "New Data Msg.lvclass"

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Child of AM2.png"> </p>

As we mentioned before, Calling Actor only care about the method/action of the message, it does not care about the message source. So in this "Update UI Msg.lvclass", we only have the "Do.vi" , no message sending function.


<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Child of AM3.png"> </p>

This "Update UI Msg.lvclass" (belong Calling Actor) should be send (in another way enqueued by Nested Actor)

#### Create Message Implementation (Before Sending)

Since the Nested Actor has no idea how the message is processed. **Before** the nested actor is launched, we need to write the message implementation that Calling Actor is going to execute when it receives this abstract message.

LabVIEW has scripting a vi "Write New Data Msg.vi" when we create the abstract message for caller

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Message Implementation.png"> </p>

In Calling Actor before Nested Actor is launched, use "Write New Data Msg" to pass "Update UI Msg.lvclass"

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Message Implementation2.png"> </p>

This is to override the "Message.lvclass:Do.vi", and execut the "Calling Actor.lvlib:Update UI Msg.lvcalss"

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Message Implementation3.png"> </p>

More details of the code interval
* Bundle message implementation into Nested Actor Data
* The Message implementation is from Calling Actor

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Message Implementation4.png"> </p>

Now we finish how the message will be handled, then we work on how to send the message

#### Send Message Implementation

After complete how the message is handled, we then work on how to send the message from Nested Actor.

Create a method within Nested Actor.class named "Send updated String.vi" to send the data via abstract message to Calling Actor.
It needs calling actor enqueuer and message implementation (or messge to use New Data Msg.lvcall) that
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Send AM.png"> </p>

Things are a bit confusing here, we get clear again.
* The Nested Actor has a method called _Write New Data Msg.vi_ (the same name as Abstract Message)
* _Write New Data Msg.vi_ has a input _New Data Msg_ (which is also Nested Actor Data element)
* In Calling Actor we have a method _Update UI_ and a message _Update UI Msg.lvclass_
* In Calling Actor we send/connect _Update UI Msg.lvclass_ to _New Data Msg_ connector of _Write New Data Msg.vi_

At this moment, we finish the method for send Message Implementation.
Then we need to create a message for this method. (It is tricky part for me at all time, every method needs a message. And we need to create a message for message methods/operations)

Right click _Send Update String.vi_ -> Actor Framework -> Create Message

Then go find the message for this Actor and place "Send Send Updated String.vi"
in the event structure of Nested Actor Core.

We do not need the "Read Caller Enqueuer.vi" anymore, it is handled within the method of "Send updated String.vi"

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Send AM2.png"> </p>

#### Finial Test

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Abstract Message/Final Test.png"> </p>

Although we still use the function "Read Caller Enqueuer", but the Abstract Message worked as medium that seperated the Calling Actor and Nested Actor.

It is not straight forward for me even after many days of thinking, thankfully the new function "Interface" make the things a bit easier

### Interface Method

Interface can be considered as a class without attributes and with all methods being abstract. Interface is a definition class:
* No code
* No data structure 
* Only empty methods to override
* Own data type

#### Create UI

It is only available at LabVIEW 2020, the code is written as LabVIEW 2020 SP1.
This is based on the UI Template, which is the VI package we used in Chapter 8.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Actor from Template.png"> </p>

Only create the Event Support for the Calling Actor string indicator (Data Received)

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Add UI Event.png"> </p>

#### Create Interface

It is recommended to add an Interface Library first for file organization.

Create Interface as creating class in Project and named the interface as "Send String".

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/New Interface.png"> </p>

Then Create a "Data Received" method within Interface, since it is a abstract method, we need to use "VI" from Dynamic Dispatch Template

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Interface Method.png"> </p>

#### Set Calling Actor to Inheritance Interface

As mentioned, Interface only define a abstract method, so we need Calling to inherit the Interface.

Change Calling Actor Parent Interface to "Send String"

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Interface Inheritance.png"> </p>

Then Error will occur and we need to develop functions to override

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Inheritance Error.png"> </p>

In Calling Actor.lvclass, right click New -> VI for Override ->

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Inheritance Override.png"> </p>

The function/method  with "*" in front needs to be override

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Interface Inheritance2.png"> </p>

As we already create the event to update Calling Actor front panel, we could generate the event and pass the "Updated Data" to the event

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Inheritance Override2.png"> </p>

The method implementation is finished. Then we need a message to execute this method from **Interface.lvlib**

#### Create Message for Nested Actor

Create the message for Data Received.vi within Interface.lvlib

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Override Message.png"> </p>

It is the Nested Actor's task to send the "Data Update" message.
With the help of Read Caller Enqueuer, we could simply put the interface within Nested Actor Event case.

But for normalization and future coding, it is better to
* Create a Nested Actor own method to perform "sending message"
* Create a message for above method
* Send above message within Nested Actor

So we create a Nested Actor method named as "Data Update.vi", within this method we read caller enqueuer and use "Send Data Receive" from Interface libray

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Nested Actor Method.png"> </p>

Then Create Msg for Data Update method within Nested Actor and put it into Nested Actor event structure.
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/9/Interface/Nested Actor Message.png"> </p>
