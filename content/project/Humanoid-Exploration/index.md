---
type: project
title: SemOpenVoc-Nav
summary: Open-Vocabulary Semantic Navigation for Indoor Environments
tags:
  - C++
  - Python
  - ROS 2
  - RTABMap
  - Nav2

date: 2026-02-10
weight: 1
share: false
links:
  - name: Code
    url: https://github.com/kjyothiswaroop/WinterProject
    icon: github
    icon_pack: fab
author: Jyothi Swaroop
video: https://github.com/user-attachments/assets/1fc9d642-b2b2-4c16-9f32-6f666eb82d2c
project_title: 'Autonomous Humanoid Exploration: Visual SLAM and Navigation on the Unitree G1'
---

-------------------------------------------------------------------------
## Overview
This project enables the **Unitree G1 29-DOF humanoid robot** to autonomously explore and navigate indoor environments. The G1 runs its own control stack over **CycloneDDS**, which is incompatible with the standard **FastDDS** middleware used by most ROS 2 packages. A major part of the work was building a reliable DDS bridge to bring the robot's sensor data into a separate ROS 2 domain where **RTABMap** and **Nav2** could run. The robot uses an onboard **Intel RealSense D435** camera for visual SLAM and depth-based obstacle avoidance — it has no lidar.

The main challenges in this project are:
- Bridging two incompatible DDS implementations (CycloneDDS ↔ FastDDS) across ROS 2 domains.
- Running visual SLAM on a walking humanoid with no lidar sensor.
- Adapting the Nav2 stack, which is typically designed for wheeled robots, for a humanoid platform.

-------------------------------------------------------------------------
## System Components

| Component | Description |
|-----------|-------------|
| **Robot** | Unitree G1 29-DOF Humanoid |
| **Camera** | Intel RealSense D435 (RGB-D + IMU) |
| **Middleware** | ROS 2 Kilted with DDS Domain Bridge |
| **SLAM** | RTABMap (visual SLAM with stereo infrared) |
| **Navigation** | Nav2 with MPPI controller |
| **IMU Fusion** | Madgwick filter on D435 IMU data |
| **Depth-to-Scan** | depthimage\_to\_laserscan for Nav2 compatibility |

-------------------------------------------------------------------------
## Architecture

The system runs across **two ROS 2 DDS domains**:

- **Domain 0 (Robot):** The G1 publishes its low-level state and camera streams over CycloneDDS.
- **Domain 1 (Navigation):** RTABMap, Nav2, and all planning/control nodes run here over FastDDS.

A **DDS domain bridge** relays key topics between the two domains — `joint_states`, `tf`, `tf_static`, and `cmd_vel`. On the robot side, a custom **jsPublisher** node converts the Unitree-specific `LowState` message into standard ROS 2 `JointState` messages with all 29 joints, so the robot can be visualized and tracked in RViz.

-------------------------------------------------------------------------
## SLAM Pipeline

The D435 camera does not have a lidar, so all mapping is done visually using **RTABMap**.

### Infrared Stereo Mode
- The D435's **IR emitter is disabled**, and the left and right infrared cameras are used as a stereo pair for depth estimation.
- This avoids synchronization issues that occur when using the RGB stream alongside active depth.

### Visual Odometry
- **RTABMap's RGBD odometry** node fuses the infrared images, depth stream, and IMU data to estimate the robot's pose.
- The IMU data from the D435 is first filtered through a **Madgwick filter** node before being fed to the odometry.
- `wait_imu_to_init` is enabled so the odometry waits for valid IMU data before starting.

### Mapping and Localization
- RTABMap builds an occupancy grid map in real time as the robot walks around.
- It publishes the `map → odom` transform, so Nav2 does not need to run its own localization (AMCL is disabled).
- For repeat visits, the system can switch to **localization-only mode** using a previously saved map database.

### Depth to LaserScan
- Since Nav2's costmaps and collision monitor expect a `/scan` topic, the **depthimage\_to\_laserscan** node converts the depth image into a 2D `LaserScan` message.
- Scan range is set to 0.2m – 4.0m.

-------------------------------------------------------------------------
## Nav2 Integration

Getting Nav2 to work on a humanoid required some adaptation since the stack is built with wheeled robots in mind.

### Motion Model
- The G1's walking is abstracted as a **differential drive** model for Nav2's planners and controllers.
- Velocity commands (`cmd_vel`) are sent as `TwistStamped` messages and bridged back to Domain 0 for execution.

### Path Planning
- **Global planner:** NavFn with Dijkstra's algorithm.
- **Local controller:** MPPI (Model Predictive Path Integral) — samples 2000 candidate trajectories over a 2.8-second horizon at 20 Hz.
- The MPPI controller uses multiple cost critics for path following, collision avoidance, goal reaching, and preferring forward motion.

### Costmaps
- **Local costmap:** 3m × 3m rolling window around the robot, updated at 5 Hz from the laser scan, with a voxel layer for 3D obstacle detection.
- **Global costmap:** Full map with static and obstacle layers, updated at 1 Hz.
- Both use an **inflation radius of 0.3m** around the robot footprint (~0.22m radius).

### Collision Monitor
- A collision monitor node watches the `/scan` topic and reduces velocity when the robot approaches obstacles.
- If the `/scan` topic goes missing (e.g., camera disconnect), the monitor blocks all velocity commands as a safety measure.

### Velocity Pipeline
The full command chain: `controller_server → cmd_vel_nav → velocity_smoother → cmd_vel_smoothed → collision_monitor → cmd_vel`

-------------------------------------------------------------------------
## Locomotion and Teleoperation

- The G1's high-level control API accepts velocity commands to walk in any direction.
- For manual control, velocity commands can be sent from a joystick or keyboard in Domain 1 and are bridged to the robot in Domain 0.
- The same `cmd_vel` interface is shared between teleoperation and autonomous navigation, making it easy to switch between the two.

-------------------------------------------------------------------------
## Key Challenges Solved

- Bridged CycloneDDS and FastDDS across two ROS 2 domains with proper QoS handling for `tf_static` (transient local durability).
- Got RTABMap visual SLAM working on a walking humanoid using infrared stereo to avoid RGB sync issues.
- Converted depth images to laser scans so Nav2's costmaps and collision monitor could function without a lidar.
- Tuned Nav2's MPPI controller for humanoid walking speeds and dynamics.
- Built a joint state bridge that converts Unitree's proprietary `LowState` messages into standard ROS 2 `JointState` for all 29 joints.

-------------------------------------------------------------------------
## Ongoing Work

- **Semantic SLAM:** Extending the current mapping pipeline to include semantic labels in the map, so the robot can reason about *what* objects are in the environment rather than just treating everything as occupied or free space.
- **NaVILA Integration:** Implementing the [NaVILA](https://navila-bot.github.io/) Vision-Language-Action model for navigation and comparing it against the conventional Nav2 stack. The goal is to evaluate how a VLA-based approach — where a single model takes in camera images and language instructions to directly output actions — stacks up against the traditional perception → planning → control pipeline in terms of generalization and robustness.

-------------------------------------------------------------------------
## Acknowledgements
This project is being developed as a **Winter independent project** at Northwestern University as part of the **MS in Robotics** program.
