---
type: project
title: ZippyX
summary: Vertical Storage and Retrival system for a commercial quick commerce store 
tags:
  - C++
  - TCP/IP
  - Behaviour Trees
  - Fleet Management System

date: 2024-02-03
weight: 3
share: false
links:
  - name: Video
    url: https://drive.google.com/file/d/1sm7AgbkE-j03x8Ooj6eiD8acjOrIOoGh/view?usp=sharing
    icon: external-link-alt
    icon_pack: fas
# image: "https://www.youtube.com/watch?v=KEsO1CyqTHA"
author: Jyothi Swaroop
project_title: 'ZippyX: Co-ordinated storage and retrival with a fleet of robots.'
---

--------------------------------------------------------------------------------------------
## Overview
This project presents a proof-of-concept for a coordinated vertical storage and retrieval system implemented using a fleet of autonomous robots. The system employs two specialized robots: one operating between the packing area and temporary storage, and the other handling transfers from temporary storage to the customer area. Each robot functions independently within its designated zone, maximizing throughput and operational efficiency. A centralized fleet management system coordinates task allocation, monitors robot status, and issues control commands—such as actuating the robot’s scissor lift—via TCP/IP communication.

---------------------------------------------------------------------------------------------
## Workflow
- A customer order is received by the Warehouse Control System (WCS).
- The order is packed into a crate at the packing station.
- Robot 1 transports the crate from the packing area to temporary storage.
- Robot 2 retrieves the crate from temporary storage and delivers it to the customer window.
- Upon arrival, the Fleet Management System commands the glass doors to open.
- The robot raises its scissor lift, allowing the customer to retrieve the items.
- After order completion is confirmed, the robot lowers the lift, the doors close, and the crate is returned to the empty-crate area.

All robot actions and customer-window interactions are coordinated by a centralized Fleet Management System.



-----------------------------------------------------------------------------------------------
## Fleet Management System

The Fleet Management System(FMS) central software written in C++ that handles communication with robots, decision making, multi-agent path planning via multiple threads to achieve maximum efficiency.

### Robot Communication
- TCP/IP is used for communication.
- A predefined set of variables are decided for the order of the TCP/IP communication.
- Disconnections and reconnections are automatically handled.

### Decision Making
- Behaviour Trees are used.
- Simple set of Sequence and Parallel nodes(not ROS Nodes) are used to achieve this.
- Each node type has a callback that is executed

### Multi-Agent Path Planning
- Robots can switch areas if required and this required safe path plannning.
- An optimal edge graph is designed for the entire barcode area to achieve optimal paths and high throughputs.

-------------------------------------------------------------------------------------------------
## Contributions

- Developed the behaviour trees and callback functions for the entire system.
- Developed all the custom interfacing between the FMS, robots and the customer window hardware.
- Supported remote deployment of the project in Netherlands from Australia!

---------------------------------------------------------------------------------------------------
## Acknowledgments

This project was developed as a part of my job at *`Addverb Technologies`* when I worked there as a Robotics Engineer. I was entirely responsible for the development, testing and deployment of the FMS system. I thank my colleague and friend Hakesh Bonigala for his support and leading the development efforts on the ZippyX robots.