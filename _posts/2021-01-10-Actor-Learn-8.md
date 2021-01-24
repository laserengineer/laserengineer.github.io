---
title: LabVIEW AF_8 User Interfaces in Actor Framework and User Event Scripting
date: 2021-01-22
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

## User Interfaces in Actor Framework and User Event Scripting

<span style="color: Cyan">The most challenging part of this post is to understand the object orientated design correctly.</span>

<span style="color: Cyan">The reason for Actro.Chat Window Model (Top nested Actor, splitting User Interfaces and the business Logic. No need to recode the functionality when UI changes, only functions related to front panel controls and indicators are needed. All the hard work can remain the same in a completely different UI actor  ) </span>

<span style="color: Cyan">The reason for Actor.Chat Room Controller is reserved for future update (User control layer and UI Abstraction Layer)</span>

<span style="color: Cyan">The reason for Actro.Chat Window Panel.UI (Concrete UI Implementation, could be modified separately updated in the future)</span>


### Chat Room Actor Nesting Hierarchy

High cohesion and low coupling distinct modular design. AF works with object-orientated programing, we have the power of inheritance.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Actor Hierarchy.png"> </p>

1. Actor.Server
  * Launch New Chat Window()
  * Send Global Chat()

> Server launches 'N' number of Chat Window Models

2. Actor.Chat Window Model
  * Format Messages()
  * Business Logic Methods()

> Chat Window Model contains all of the business logic. And each Chat Window Model Launches 1 user interface

3. Actor.Chat Room Controller
  * Update Addresses()
  * Update Message()
  * Update Who's Online()
  * Send Message Pressed()
  * Status Update

> The Chat Room Controller defines all of the messages and methods for the child interfaces to override

4. Actor.Chat Window Panel.UI
  * Update Addresses()
  * Update Message()
  * Update Who's Online

> The Chat Window Panel is a concrete implementation of a UI. However, to make a new UI. You would just have to make a sibling actor and override the update methods. Apart from updating the UI. UI actors should have no additional responsibility

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Actor Layers.png"> </p>

### User Events for UI
Install VI Package events_for_ui_actor_indicators-1.0.0.6. [Link](https://forums.ni.com/t5/Actor-Framework-Documents/Events-for-UI-Actor-Indicators/ta-p/3869260?profile.language=en)

The VI screen show as following, it is not an official VI package.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Actor Layers.png"> </p>

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/vi package.png"> </p>

Create UI Actor from UI template

<p align="left"> <img src="/assets/images/LabVIEW Actor Framework/8/UI template.png"> </p>

<p align="left"> <img src="/assets/images/LabVIEW Actor Framework/8/Chat Window Panel Actor.png"> </p>

The new Chat Window panel actor core has the override for and user event Register ,response and unregister.
The Front Window will show when called and close afterwards

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/UI Actor Core.png"> </p>

#### Check Pre Launch Init.vi
Stop user event is created within Pre Launch Init.vi and destroy the event in case of error.

Stop core will use the same "Destroy Events", generate the stop event first to stop the help loop and then destroy the event.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/UI Actor Event Stop.png"> </p>

#### Develop UI Actor Core  

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/UI Design.png"> </p>

Right click Message and select "Add Event Support"
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Add Event.png"> </p>
The installed package will automatically add new user event to the diagram

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Auto add user event.png"> </p>

Create event, register event, unregister event and destroy event has been automatically handle. We need create a method to generate this event.

"Chat Window Panel.vlclass" should override the method in "Chat Room Controller.lvclass", change UI interface to chat room controller.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Update Inheritance.png"> </p>

Within UI actor create "Update Message" to override controller "Update Message" method.  
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Override Message Update.png"> </p>


In Chat Room Model, the Chat Window Panel override the parent lvclass ChatRoom Controller lvclass

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/New Nested Actor.png"> </p>

Disable the Chat Room Controller core show font panel, it is called by Actor Chat Window Panel actor core. Or you can delete the invoke node like Tom McQuillan did.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Disable Chat Room Controller Call.png"> </p>

PS:
Small tricks to hide launch root actor front panel in development environment
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/8/Launch Root Actor Trick.png "> </p>
