---
title: LabVIEW Actor Framework_1
categories:
  - Learn
tags:
  - LabVIEW
  - Actor
---
## Recap of Modular Applications in LabVIEW

### Sample Code
[Github Link](https://github.com/laserengineer/LabVIEW-Study)
The right Structure is the easy solution for LabVIEW CLA exam. Six parallel process including:
* Controller/Message Handler
* User Console (GUI)
    * Simulator
    * Physical
* Display Console (GUI)
    * Simulator
    * Physical
* Sensor Interface (Optional)
    * Simulator
    * Hardware
* DB/Configuration File
    * Simulator
    * Embedded
* Error Handling

This a good solution for CLA exam but not a idle solution for real project as code duplication. Each module has an event handling loop (EHL) and an Message Handling Loop (MHL). Many of the EHL are only for exit the application.


### Modular Structure Requirements

> * High Cohesion
>   * Each module should only have a definitive purpose
> * Low Coupling
>   * A module shouldn't be dependent on another module in order to operate.

* Cohension is about how the module is written (intra-module) and self ccompleteness for single task
    * Readability
    * Maintainability
    * Reusability


* Coupling is about how the module relies on other modules (inter-module )
    * Maintainability
    * Testability (unit testing)
    * Readability

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/1/Modular Structure.png"> </p>

Sample Project Structure:
* User Interface: Event Handling Loop (EHL)
* User Console (GUI)