---
title: "Force Feedback Teleoperation"
date: 2024-06-16
excerpt_separator: ""
categories:
  - blog
tags:
  - Projects
  - Grad School
  - Robotics
---

# Project Overview: Haptic Robotic Control

[cite_start]In this graduate-level project, my team and I developed a force-feedback teleoperation system using two identical 2R robotic arms[cite: 17, 41]. [cite_start]The system operates on a "Leader-Follower" principle: a user manipulates the leader arm, and the follower arm mimics those movements in a remote workspace[cite: 22, 24]. [cite_start]The primary goal was to create a haptic loop where the user could "feel" the environment; if the follower arm collided with an obstacle, the system communicated that resistance back to the leader arm, making it physically harder to move[cite: 27, 28].

#### Design Objectives
To ensure a high-fidelity experience, the system had to meet several rigorous specifications:
* [cite_start]**Tracking Precision:** End effector offset error of less than 5mm in free space[cite: 33].
* [cite_start]**Responsiveness:** The follower arm had to match the leader's position within 0.5 seconds[cite: 34].
* [cite_start]**Haptic Fidelity:** The leader arm needed to provide approximately 50 N/m of resistive force feedback upon contact[cite: 37].
* [cite_start]**Stability:** Both arms required an oscillation time of under 1 second for any user input[cite: 36].

![Robot Setup Photo](/assets/images/teleop/robot_setup.png){: width="90%"}

## System Configuration and Methods

[cite_start]We utilized two 2R robot arms, each equipped with two Dynamixel MX-28AR motors mounted on an optical breadboard[cite: 41, 44]. [cite_start]For our testing, the arm on the left served as the leader, while the arm on the right acted as the follower[cite: 46].

[cite_start]As part of this project, I specifically headed the implementation and gain tuning for the **Impedance Control** system. We explored three distinct control architectures to compare their effectiveness:

1.  [cite_start]**Decentralized Position and Velocity Control:** A straightforward approach where each motor was adjusted independently based on encoder feedback[cite: 51, 56].
2.  [cite_start]**Gain Scheduling:** An enhancement to the standard PD control that adjusted proportional gains based on the duration of the tracking error, providing a more "aggressive" resistance when obstacles were hit[cite: 98, 101].
3.  [cite_start]**Impedance Control:** A centralized method designed to set virtual stiffness and damping characteristics[cite: 66]. [cite_start]This involved complex calculations to convert joint space variables to task space coordinates and computing inverse dynamics[cite: 68, 71].

![Control Block Diagram](/assets/images/teleop/block_diagram.png){: width="90%"}

## Performance Analysis & Results

[cite_start]Our testing revealed that while simple PD control was easy to implement, it failed to meet our 5mm tracking requirement and provided poor force feedback—users had to move the leader arm significantly far from the follower to feel any resistance[cite: 213, 316].

#### Free Space Tracking
[cite_start]The **Gain Scheduling** controller performed the best in free space, nearly satisfying the < 5mm error requirement across the entire path[cite: 214]. [cite_start]The **Impedance Control** system, which I worked on, faced challenges with tracking Joint 2 accurately, likely due to the computational cost of calculating centralized arm dynamics and errors in the inverse Jacobian implementation[cite: 130, 249, 250].

#### Obstacle Interaction
The highlight of the project was the performance of the Gain Scheduling algorithm during contact. [cite_start]We observed that the leader arm's trajectory would "flatten" when the follower hit an object, indicating the user's hand was being physically slowed by the increased motor torque[cite: 277, 279]. [cite_start]This provided the intuitive "haptic" feel we were aiming for[cite: 280]. [cite_start]In contrast, the Impedance controller became unstable during certain singular configurations, leading to erratic movements[cite: 267, 300].

![Position Error Graph](/assets/images/teleop/error_plots.png){: width="90%"}

## Challenges and Lessons Learned

The greatest hurdle we faced was **latency**. [cite_start]Motor read and write commands introduced over 40ms of delay per iteration, capping our control frequency at 25 Hz[cite: 371, 372]. [cite_start]This delay often resulted in "jerky" movements and limited our ability to increase damping without destabilizing the system[cite: 373]. 

[cite_start]Additionally, we discovered that setting the leader arm's derivative gain to zero was actually beneficial[cite: 369]. [cite_start]Because the user's hand acts as a natural damper, it absorbs shocks that would normally require complex software damping[cite: 370].

[cite_start]In the future, I would look toward using high-frequency microcontrollers like an Arduino or Teensy to boost the control loop frequency and implementing low-pass filters to mitigate the sensor noise we encountered[cite: 375, 377, 378].

***

### Team Contributions
* [cite_start]**Jed:** CAD, assembly, and Impedance gain tuning.
* [cite_start]**Dylan:** PD and Gain Scheduling design and coding.
* **Harry:** Impedance control coding and gain tuning.
* [cite_start]**Alexander:** Impedance design/code and PD gain tuning.

### References
* Hirzinger, G., et al. (2005). [cite_start]"Rokviss-robotics component verification on ISS"[cite: 394].
* Bruno Siciliano, et al. (2010). "Robotics: Modelling, Planning and Control"[cite: 402].
