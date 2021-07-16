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

[Learn notes for Actor Framework Basic by Moore Good Ideas)](https://www.mooregoodideas.com/actor-framework/basics/AF-basics-part-1/)

Learning notes for Actor Model again.
It has been quite a long journey curve for me to learn LabVIEW Actor Framework. It takes a lot of courage to use this framework in a real time challenging project.

That has being well said
“If you cannot do great things, do small things in a great way.”

So let us go back to the beginning of "Queued Message Handler" (QMH)

# Part 1 - the Basics  
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

1. The open Queue: Passing a basic LabVIEW Queue Reference around is a not smart move. It could happens that something else will destroys the Queue. Or something else decides its message has higher priority to enqueue it at the beginning instead of end, or something even worse-> flushes the queue first.

## Actor system
