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

![12-12-56-25_BACnet Stack Layers no BG](/assets/12-12-56-25_BACnet%20Stack%20Layers%20no%20BG.png)


The BACnet is abbreviation for Building Automation and Control Network and this post has 0 intention to cover the details for the technology. It is served as learning notes for potential BACnet LabVIEW development. In general, two types of sub communication protocals are used. There are no proprietary chip sets or special electronics required to implement. Thus, BACnet meets the needs of users, integrators, and equipment vendors. BACnet defines a set of standard objects whose properties represent the information that is exchanged between components of the system network and an application layer protocaol that is used to access and maniplulate this information. 

- [x] **BACnet serial MSTP**
- [x] **BACnet/IP network**

The communication protocal consists a set of rules that monitors data that is exchanged between devices, enubale communication among devices in a network. 

To communication between devices, the BACnet protocal uses **services** and **objects**. One device has multiple objects and one object has multiple property 

* **Service**
  A service is a means or interface between two devices to access and process information, request to perform an action, or inform the devices that some event has taken place. These are used for discovering devices and objects, and for sharing data. BACnet protocol defines “services”. These include object access services, alarm and event services, file access services, and a few more. Object access services are the most commonly used since these provide the fundamental “read/write” access to object properties
  Some services example
>* Who-Is (Device and Object Discovery)
>* I-Am (Device and Object Discovery)
>* etc.

* **Object**
  BACnet devices are defined on the network as a collection of “objects”.The general reference to sensors, actuators, and other functional elements that make up a BACnet device. The objects fall into categories pecified by BACnet protocol. Analog Input object and Analog Ouput object are a couple of the most commonly used objects.

* **Object Property**

  Each object has/include several properties required by BACnet protocol.The messages on the network deal with reading and writing Object Properties. The most commonly used protperty is **Present Value**. An object along with its set of properties describes the current status of a device to the other devices on the network. Objects enable ,reading, writing and perform the desired functions of a device. 


>* Analog Input
>* Analog Output
>* Analog Value
>* Binary Input
>* Binary Output
>* Device
>* etc.

The BACnet MS/TP protocol uses EIA-485 (RS-485) as the physical layer standard for data transmission. Controllers also use the BACnet MS/TP protocol over an RS-485 standard for communicating with third party routers, gateways, or master controllers. 

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