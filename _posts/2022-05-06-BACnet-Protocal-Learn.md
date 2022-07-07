---
title: BACnet-Protocal-Learn
date: 2022-07-07
categories:
  - Study
  - LabVIEW
  - BACnet MS/TP
tags:
  - LabVIEW
  - BACnet MS/TP
---


![BACnet-Logo-R](/assets/BACnet-Logo-R.gif)
# Part 1 - Theory about BACnet  
## What is Bacnet

![12-12-56-25_BACnet Stack Layers no BG](/assets/12-12-56-25_BACnet%20Stack%20Layers%20no%20BG.png)

This post has **ZERO intention** to cover the details for the technology. It solely served as learning notes for potential BACnet COMM module development in LabVIEW development.

The BACnet is abbreviation for Building Automation and Control Network and it is both an international (ISO) and ANSI standard for interoperability between cooperating building automation devices.BACnet addresses the goal of interoperability by defining a generalized model of how automation devices work, a method for describing the information that they contain, and a method for describing protocols that one device can use to ask another device to perform some desired action. 

<p align="center">

![BACnet communication architecture](/assets/BACnet%20communication%20architecture.PNG)
</p>

BACnet uses an object-oriented model for abstracting and representing information.The object-based model has been proven to be both robust and reliable while providing a high degree of backward and forward complatibility. 

  In general, two types of networking options are used. There are no proprietary chip sets or special electronics required to implement. Thus, BACnet meets the needs of users, integrators, and equipment vendors. BACnet defines a set of standard objects whose properties represent the information that is exchanged between components of the system network and an application layer protocaol that is used to access and maniplulate this information. 

- [x] **BACnet serial MSTP**
- [x] **BACnet/IP network**

The communication protocal consists a set of rules that monitors data that is exchanged between devices, enubale communication among devices in a network. 

To communication between devices, the BACnet protocal uses **services** and **objects**. One device could have a collection of objects and each object has a collection of properties. Each property includes at least a name and a value. For example one of the standard BACnet objects is the Analog Input Object which represents an analog sensor input such as a thermistor.   

Below diagram of an Analog Input Object that includes Description, Device_Type, Units are set during installation. Others, including Present_Value and Out_Of_Service provides status about the sensor input represent by the Analog Input Objects. An Analog input object can have up to 25 properties. in this example, a query about the Present_Value Property of this Analog Input Object would get the reply "68.0".

![BACnet AI Objects](/assets/BACnet%20AI%20Objects.gif)


* **Devices**
  
  A BACnet device is often comprised of a microprocessor-based controller and software combination is the designed to undertand and use the BACnet protocal. Each device contains a **device object** (and other objects) that defines certain device information including the device object identifier or instance number. The instance number must be field-configurable to be unique across the entire BACnet network. For brevity this number called *device instance*. 
  
  BACnet requires one Device Object to be present in every BACnet device.he Device Object's Instance number must be unique across the entire BACnet internetwork because it is used to uniquely identify the BACnet devices. It may be used to conveniently identify the BACnet device from other devices during installation.

    **Properties of the Device Object.**


| PROPERTY                        | BACnet   | EXAMPLE                                          |
|---------------------------------|----------|--------------------------------------------------|
| Object_Identifier               | Required | Device #1076                                     |
| Object_Name                     | Required | "Office 36 DD Control"                           |
| Object_Type                     | Required | Device                                           |
| System_Status                   | Required | Operational (plus others)                        |
| Vendor_Name                     | Required | "Alerton Technologies, Inc."                     |
| Vendor_Identifier               | Required | Alerton                                          |
| Model_Name                      | Required | "VAV-DD Controller"                              |
| Firmware_Revision               | Required | "1.0"                                            |
| Application_Software_Version    | Required | "Dual-Duct DDC"                                  |
| Location                        | Optional | "Office 36, Floor 3"                             |
| Description                     | Optional | "(on network 5)"                                 |
| Protocol_Version                | Required | 1 (BACnet protocol version)                      |
| Protocol_Conformance_Class      | Required | 2                                                |
| Protocol_Services_Supported     | Required | readProperty, writeProperty, atomicWriteFile,... |
| Protocol_Object_Types_Supported | Required | Analog Input, Analog Output,...                  |
| Object_List                     | Required | Analog Input #1, Analog Input #2, ...            |
| Max_APDU_Length_Supported       | Required | 50 (bytes or characters)                         |
| Segmentation_Supported          | Required | No                                               |
| VT_Classes_Supported            | Optional | n/a                                              |
| Active_VT_Sessions              | Optional | n/a                                              |
| Local_Time                      | Optional | 12:30:15.22                                      |
| Local_Date                      | Optional | Tuesday, March 12, 1996                          |
| UTC_Offset                      | Optional | +480 (minutes from GMT/UTM)                      |
| Daylight_Savings_Status         | Optional | False (not in effect)                            |
| APDU_Segment_Timeout            | Optional | n/a                                              |
| APDU_Timeout                    | Required | 3000 milliseconds                                |
| Number_Of_APDU_Retries          | Required | 0                                                |
| List_Of_Session_Keys            | Optional | n/a                                              |
| Time_Synchronization_Recipients | Optional | n/a                                              |
| Max_Master                      | Optional | n/a                                              |
| Max_Info_Frames                 | Optional | n/a                                              |
| Device_Address_Binding          | Required | None                                             |





* **Devices Interoperability**
  BACnet divides the task of device interoperability into three distinct areas: Objects (information), Services (action requests), and Transport systems (internetworking, electronic messages). BACnet defines methods and requirements for implementation of each of these areas

* **Object**
  BACnet devices are defined on the network as a collection of “objects”. The BACnet standard defines 54 different standard object types and implementation of a given device may make use of arbitrary combinations of these standard object types to represent information and control logic.
  
  Each oject is identified with an object identifier and an object identifier is a 32-bit binary number. 
  The general reference to sensors, actuators, and other functional elements that make up a BACnet device. The objects fall into categories specified by BACnet protocol. Analog Input object and Analog Ouput object are a couple of the most commonly used objects.


**Standard BACnet Objects**

| OBJECT             | EXAMPLE OF USE                                                                                                                                                                                                              |
|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Analog Input       | Sensor input                                                                                                                                                                                                                |
| Analog Output      | Control output                                                                                                                                                                                                              |
| Analog Value       | Setpoint or other analog control system parameter                                                                                                                                                                           |
| Binary Input       | Switch input                                                                                                                                                                                                                |
| Binary Output      | Relay output                                                                                                                                                                                                                |
| Binary Value       | Binary (digital) control system parameter                                                                                                                                                                                   |
| Calendar           | Defines a list of dates, such as holidays or special events, for scheduling.                                                                                                                                                |
| Command            | Writes multiple values to multiple objects in multiple devices to accomplish a specific purpose, such as day-mode to night-mode, or emergency mode.                                                                         |
| Device             | Properties tell what objects and services the device supports, and other device-specific information such as vendor, firmware revision, etc.                                                                                |
| Event Enrollment   | Describes an event that might be an error condition (e.g., "Input out of range") or an alarm that other devices to know about. It can directly tell one device or use a Notification Class object to tell multiple devices. |
| File               | Allows read and write access to data files supported by the device.                                                                                                                                                         |
| Group              | Provides access to multiple properties of multiple objects in a read single operation.                                                                                                                                      |
| Loop               | Provides standardized access to a "control loop."                                                                                                                                                                           |
| Multi-state Input  | Represents the status of a multiple-state process, such as a refrigerator's On, Off, and Defrost cycles.                                                                                                                    |
| Multi-state Output | Represents the desired state of a multiple-state process (such as It's Time to Cool, It's Cold Enough and it's Time to Defrost).                                                                                            |
| Notification Class | Contains a list of devices to be informed if an Event Enrollment object determines that a warning or alarm message needs to be sent.                                                                                        |
| Program            | Allows a program running in the device to be started, stopped, loaded and unloaded, and reports the present status of the program.                                                                                          |
| Schedule           | Defines a weekly schedule of operations (performed by writing to specified list of objects with exceptions such as holidays. Can use a Calendar object for the exceptions.                                                  |


* **Object Property**

  Each object has/includes several properties required by BACnet protocol.Each property contains two pieces of information.
  * Property Identifier
  * Property Value 
  
  The messages on the network deal with reading and writing Object Properties. The most commonly used protperty is **Present Value**. An object along with its set of properties describes the current status of a device to the other devices on the network. Objects enable ,reading, writing and perform the desired functions of a device. 


>* Analog Input
>* Analog Output
>* Analog Value
>* Binary Input
>* Binary Output
>* Device
>* etc.


![BACnet Objects Properties](/assets/BACnet%20Objects%20Properties.PNG)


**Properties of the Analog Input Object.**


| PROPERTY           | BACnet   | EXAMPLE                                                      |
|--------------------|----------|--------------------------------------------------------------|
| Object_Identifier  | Required | Analog Input #1                                              |
| Object_Name        | Required | "AI 01"                                                      |
| Object_Type        | Required | Analog Input                                                 |
| Present_Value      | Required | 68.0                                                         |
| Description        | Optional | "Outside Air Temperature"                                    |
| Device_Type        | Optional | "10k Thermistor"                                             |
| Status_Flags       | Required | In_Alarm, Fault, Overridden, Out_Of_Service flags            |
| Event_State        | Required | Normal (plus various problem-reporting states)               |
| Reliability        | Optional | No_Fault_Detected (plus various fault conditions)            |
| Out_Of_Service     | Required | False                                                        |
| Update_Interval    | Optional | 1.00 (seconds)                                               |
| Units              | Required | Degrees-Fahrenheit                                           |
| Min_Pres_Value     | Optional | -100.0, minimum reliably read value                          |
| Max_Pres_Value     | Optional | +300.0, maximum reliably read value                          |
| Resolution         | Optional | 0.1                                                          |
| COV_Increment      | Optional | Notify if Present_Value changes by increment: 0.5            |
| Time_Delay         | Optional | Seconds to wait before detecting out-of-range: 5             |
| Notification_Class | Optional | Send COV notification to Notification Class Object: 2        |
| High_Limit         | Optional | +215.0, Upper normal range                                   |
| Low_Limit          | Optional | -45.0, Lower normal range                                    |
| Deadband           | Optional | 0.1                                                          |
| Limit_Enable       | Optional | Enable High-limit-reporting, Low-limit-reporting.            |
| Event_Enable       | Optional | Enable To_Offnormal, To_Fault, To_Normal change reporting.   |
| Acked_Transitions  | Optional | Flags indicating received acknowledgments for above changes. |
| Notify_Type        | Optional | Events or Alarms                                             |

* **Service**

  BACnet services are formal requests that one BACnet device sends to another BACnet device to ask it to do something. 
  A service is a means or interface between two devices to access and process information, request to perform an action, or inform the devices that some event has taken place. These are used for discovering devices and objects, and for sharing data. BACnet protocol defines “services”. These include object access services, alarm and event services, file access services, and a few more. Object access services are the most commonly used since these provide the fundamental “read/write” access to object properties
  Some services example
>* Who-Is (Device and Object Discovery)
>* I-Am (Device and Object Discovery)
>* etc.
![BACnet Service](/assets/BACnet%20Service.gif)
The model of objects and services is realized by encoding messages into a stream of numeric codes that represent the desired functions or services to be performed. d. The "language" of this encoding is common to all BACnet devices. BACnet devices exchange information and do things by sending and receiving electronic messages containing this coded language. 


## Addressing
### Baud Rate 

Baud Rate is the rate at which data is communicated to the devices. You can configure the MS/TP networks to communicat at the following baud rates:

* 9.6 kbps
* 19.2 kbps
* 38.4 kbps
* 76.8 kbps 
* Auto Detect 

All the devices on the MS/TP segment must communicate at the **same** band rate. 
### Device ID & Device Instances

The Device ID (Device Object Identifier) is used in a BACnet network as the unique identifier of a specific device. The Device ID for each device must be unique on the entire BACnet network.

This is the logical address that matters to BACnet. Whether on an MS/TP link or IP network, the device instance is unique across all subnets and routed links. 
Device Instance can be in the range of 0 to 4194304(?). The device instance can be set via the device display or BACnet.



### MAC Addresses

The MAC address must be unique on a BACnet MT/TP segment. By default, the MAC address on our controllers is set to 0. A MAC address cannot be duplicated on a single MS/TP trunk. The MAC address can be set via onboard DIP switches, the device display or via BACnet. 

### Master - Slave

MS/TP stands for Master Slave Token Passing. Each device on the link is considered the “master” when it has the token. If it does not have immediate need to use the token, it is required to pass the token along to the next device. This is the “token passing” part. All devices on the link, which do not currently have the token are regarded as slaves, and are expected to listen to any messages the current master may have for it. Because all devices take turns being master, the link is effectively peer-to-peer.

0-127 addresses are for master devices and 128-254 addresses are reserved for slave devices. Only master devices can initiate requests and are part of the token passing. Slave devices cannot initiate requests for data,they only reply to messages from other master devices and are not part of the token passing.


# Part 2 - Edigit BACnet API

Help link https://endigit.com/bacnet-labview-api/detailed-help

## Design 

1. Function as a device on a BACnet serial MSTP or BACnet/IP network (replies to Who-Is with I-Am)
2. Read/write Present Value for Analog/Binary Input/Output/Value object types
3. Issue Change of Value (COV) requests to reduce network usage

## Implementation 

The Endigit BACnet API is a class-based LabVIEW driver that follows the "Initialize -> Read/Write -> Close" model. 

You will initialize the main class based on the type of BACnet device you want to be (BACnet/IP, MSTP Master, or MSTP). This initialization will return the light blue class wire, which you will use for any reads and/or writes, and finally for the close.  This initialize call spins up dynamic loops to handle the device (UDP for BACnet/IP and EIA-485 serial for MSTP) and APDU (Application Layer Protocol Data Unit) layers.


You will also need to initialize a BACnet object for **each object** you want to talk to using "Object.lvclass:Initialize B.IP Object.vi" or the generic "Object.lvclass:Initialize.vi". The class returned from this initialize will be an input to any reads or writes.


![Initialize BIP](/assets/Initialize%20BIP.png)

"IP Address" is the local IP address you want the UDP connection to be opened on.

"Local Port (0xBAC0)" is the local port you want to use to create the UDP socket. The default is 0xBAC0, but this can be any available port. You will receive messages and replies from other devices on this port.

"Instance (7)" (optional) uniquiely identifies this device object from other device objects at this IP address and port. This is up to you to define. Doesn't much matter because only one device can be on a given UDP port anyway.

"Object Name" (optional) is inserted into the "object_name" property of the device, which may be read by other devices.

"Objects" (optional) is an array of objects that you want this device to own. If you are wanting to host objects and values for other devices to read/write from/to, then initiaize them using "Object.lvclass:Initialize.vi" and pass them in here.

"BACnet Out" (output) is the BACnet class used for all other API methods.


![Initialize MSTP Master](/assets/Initialize%20MSTP%20Master.png)

"VISA" is the reference to the serial port you want your device to reside on.

"Baud Rate" is the defined baud rate for the serial network you are connecting to.

"Address" uniquely identifies your device on the serial network. Must not collide with any other devices on the network.

"BACnet Out" (output) is the BACnet class used for all other API methods.

![Initialize BACnet Object](/assets/Initialize%20BACnet%20Object.png)
![Initialize BACnetIP Object](/assets/Initialize%20BACnetIP%20Object.png)

"Remote IP Address" is the IP address of the device you want to read from or write to.

"Type" is the type of the object you want to read from or write to. Tested types with this API are Analog/Binary Input/Output/Value. You will need to get this from your specific BACnet configuration.

"Instance" uniquiely identifies the object of a given type at the specified address. You will need to get this from your specific BACnet configuration.

"Remote Port (0xBAC0)" is the port on the remote device you want to read from and write to. You can have multiple objects on a port and multiple ports on a device. You will need to get this from your specific BACnet configuration.

"Write Priority (0)" (optional) defines the relative priority of objects for arbitration. Valid options are 1-16, with 1 being the highest priority. A '0' will cause the property to not be written (less bandwidth usage), defaulting the priority to a 16.

"Object Out" (output) is the BACnet object used in the read and write methods


![BACnet Write](/assets/BACnet%20Write.png)
"BACnet In" is the BACnet class and it must be initialized before being called here.

"Object In" is the object you want to write to. See "Initialize B.IP Object.vi".

"Property (Present Value)" is the property of "Object In" that you want to write to. This enum contains all of the properties in the BACnet specification, but only Present Value has been thoroughly tested.

"Value" is the value you want to write to the object property. For analogs, this gets converted to a single. For booleans, 0 is false, everything else is true.

"Timeout (10000 ms)" (optional) defines the maximum time to wait for a reply. If no reply is received by this time, the VI returns a timeout error.

"BACnet Out" (output) is the BACnet class used for all other API methods.

![BACnet Read](/assets/BACnet%20Read.png)

"BACnet In" is the BACnet class and it must be initialized before being called here.

"Object In" is the object you want to read from. See "Initialize B.IP Object.vi".

"Property (Present Value)" is the property of "Object In" that you want to read from. This enum contains all of the properties in the BACnet specification, but only Present Value has been thoroughly tested.

"Timeout (10000 ms)" (optional) defines the maximum time to wait for a reply. If no reply is received by this time, the VI returns a timeout error.

"BACnet Out" (output) is the BACnet class used for all other API methods.

"Value" (output) is the value of the property that you read from

![BACnet Close](/assets/BACnet%20Close.png)
"BACnet In" is the BACnet class used for all other API methods. Each initialized instance should be closed when it is no longer needed.