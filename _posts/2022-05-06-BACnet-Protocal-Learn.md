---
title: BACnet_MS/TP-Protocal-Learn
date: 2021-09-02
categories:
  - Study
  - LabVIEW
  - BACnet MS/TP
tags:
  - LabVIEW
  - BACnet MS/TP
---

[Learn notes for BACnet MS/TP](https://www.neptronic.com/controls/PDF/EVC/BACnetModbus/BACnet%20MSTP%20Overview%20Manual-160405.pdf)


![BACnet-Logo-R](/assets/BACnet-Logo-R.gif)

# Part 1 - Theory about BACnet  
## What is Bacnet



The BACnet is abbreviation for Building Automation and Control Network and this post has 0 intention to cover the details for the technology. It is served as learning notes for potential BACnet LabVIEW development. In general, two types of BACnet communication protocals are used 

- [x] **BACnet serial MSTP**, it is usually the type defined cluster on the left shift register. Everything about the internal state of QMH is stored here
- [x] The **Queue**, this is how you get data into the QMH and tell QMH what task to do

it  not this document intension to cover  



![12-12-56-25_BACnet Stack Layers no BG](/assets/12-12-56-25_BACnet%20Stack%20Layers%20no%20BG.png)



QMH design pattern has the following parts
<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/13/Classic-QMH.png"> 

- [x] The **Data**, it is usually the type defined cluster on the left shift register. Everything about the internal state of QMH is stored here
- [x] The **Queue**, this is how you get data into the QMH and tell QMH what task to do
- [x] The **Message Handler**, This is the "thread" that QMH executes on. This keeps running until QMH decides that it's done. This is a while loop with case structure inside.
- [x] The **The Messages**, each message has an identifier (the string e.g. "Start", in the image) and data (variant)

### Pros for QMH

1. Data Encapsulation: Only this specific QMH has the capability to handle specific functions, no other parts of the code can come in and put QMH in an invalid state

2. Separate Thread Execution, multi-threaded application, if we need a new thread to execute something, we can just implement it as a QMH, then send it messages and get the task finished

### Cons for QMH

# Part 2 - Edigit BACnet API
