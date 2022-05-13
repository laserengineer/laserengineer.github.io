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

[Understanding BACnet](https://www.youtube.com/watch?v=oevGXrkxEos&list=PL2Sd_0UX_Ck4ezaB9vMazMCsF0GbM8C9e&index=1&ab_channel=ContemporaryControls)


# Part 1 - Theory about BACnet  
## What is Bacnet

The BACnet (Building Automation and Control Network)

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

# Part 2 - Edigit BACnet API
