---
title: RTOS-Protocal-Learn Notes
date: 2022-09-30
categories:
  - Study
  - Arduino
  - RTOS
tags:
  - Arduino
  - RTOS
---

![Real-Time OS](/assets/RTOS/free-rtos-featured-1.webp)

[Learn notes for Introduction to RTOS ](https://www.youtube.com/watch?v=F321087yYy4&list=PLEBQazB0HUyQ4hAPU1cJED6t3DU0h34bz&index=1&ab_channel=Digi-Key)

# Introduction to RTOS 
## Part 1 - What is a Real-Time Operating System (RTOS)

A General Purpose Operating System (GPOS) handles 

- [x] **Task Scheduling**
- [x] **Resource management**
- [x] **Device Drivers** 

For GPOS, the most important feature is human interaction, the scheduler is designed to prioritize such tasks that may mean some timing deadlines can be missed, pushed back and a little lag in responsiveness especially if it's not really noticed by a human is acceptable.  

The GPOS scheduler is often non-deterministic, which menas that we can't know exactly which task will execute when and for how long. It is not acceptable for strict timing deadline requirment situations like medical device or engine controller. 

A real-time operating system (RTOS) is an operating system that runs multi-threaded applications and the scheduler can guarantee meeting real-time deadlines. 

An RTOS is often a lightweight operating system (OS) designed to run on microcontrollers. Much like general purpose operating systems, they offer a scheduler to run multiple threads or tasks, resource management (such as file I/O), and device drivers. An RTOS is necessary when embedded engineers need to run several threads at the same time on a processor. 

The "real-time" part of an RTOS means that such software can meet real-time deadlines. Note that this does not mean "fast", but rather , it can guarantee that task execution time can be calculated prior to runtime. 

>Supper Loop design has very little overhead design, easy to accomplish a handful of tasks on your microcontroller. 
![Supper loop](/assets/RTOS/Super%20Loop.PNG)

No way to execute task concurently 

### RTOS usually run the tasks concurently. 

- **Task**: set of program instructions loaded in memory
- **Thread**: unit of CPU utilization with its own program counter and stack 
- **Process**: instance of a computer program, with one or more threads used to acomplish tasks

![RTOS Loop](../assets/RTOS/RTOS%20Loop.PNG)
**Publish** sub-menu.
![Different platform](../assets/RTOS/RT%20HW.PNG)
## Update a publication
