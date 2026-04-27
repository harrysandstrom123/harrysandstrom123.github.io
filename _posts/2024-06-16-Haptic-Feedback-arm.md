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

In this project for MAE 263C Robotic Controls, my team and I developed a force-feedback teleoperation system using two identical 2R robotic arms. The system operates on a "Leader-Follower" principle: a user manipulates the leader arm, and the follower arm mimics those movements in a remote workspace. The primary goal was to create a haptic loop where the user could "feel" the environment; if the follower arm collided with an obstacle, the system communicated that resistance back to the leader arm.

#### Design Objectives
To ensure a high-fidelity experience, the system had to meet several rigorous specifications:
* **Tracking Precision:** End effector offset error of less than 5mm in free space.
* **Responsiveness:** The follower arm had to match the leader's position within 0.5 seconds.
* **Haptic Fidelity:** The leader arm needed to provide approximately 50 N/m of resistive force feedback upon contact.
* **Stability:** Both arms required an oscillation time of under 1 second.

![Robot Setup Photo](/assets/images/teleop/robot_setup.jpg){: width="90%"}

## System Configuration and Methods

We utilized two 2R robot arms, each equipped with two Dynamixel MX-28AR motors mounted on an optical breadboard. While we tested decentralized PD and Gain Scheduling, a significant portion of our research focused on **Impedance Control**.

### Impedance Control Implementation
Unlike decentralized methods, impedance control is a centralized indirect force control strategy. It allows the designer to define an "apparent" task-space stiffness, damping, and inertia that the robot should exhibit. My role focused on the code and gain tuning for this implementation:

* **Task-Space Transformation:** Measured joint space positions and velocities were converted to task-space coordinates.
* **Virtual Mass Dynamics:** We utilized an inverse virtual mass matrix, $M_d^{-1}$, to convert an end-effector force into a desired acceleration.
* **The Control Pipeline:** This acceleration was then multiplied by an inverse analytical Jacobian, $J_A^{-1}(q)$, and an inverse dynamics matrix, $B(q)$, to find the appropriate joint torques.
* **Trajectory Mapping:** The kinematics of the leader arm became the trajectory of the follower, and the kinematics of the follower became the trajectory of the leader, creating a bidirectional feedback loop.

![Impedance Leader Block](/assets/images/teleop/block_diagram_leader_impedance.png){: width="90%"}

*Figure: Impedance Controller Leader Block Diagram*

![Impedance Follower Block](/assets/images/teleop/block_diagram_follower_impedance.png){: width="90%"}

*Figure: Impedance Controller Follower Block Diagram*

<!-- ![Control Block Diagram](/assets/images/teleop/block_diagram.png){: width="90%"} -->


## Performance Analysis & Results

### The Challenge with Impedance Control
While theoretically more sophisticated, the impedance controller faced implementation hurdles that impacted its real-world performance: 

* **Tracking Failure:** The controller failed to meet our 5mm tracking target, primarily due to excessive error in Joint 2. This was likely caused by inaccuracies in the implementation of the inverse analytical Jacobian and the virtual mass calculations.
* **Singular Configurations:** A critical issue was the tendency for the follower arm to move into a singular position. Once in a singularity, the motors produced chaotic torque inputs, leading to total system failure.
* **Stability vs. Noise:** To combat sensor noise, we implemented a moving average filter. However, this introduced additional delay, limiting our ability to increase damping high enough to stabilize the manipulator without causing oscillations.

![Position Error Graph Impedance](/assets/images/teleop/error_plots_impedance.png){: width="90%"}

*Figure: Impedance Control End Effector Distance Error In Free Space*

Furthermore, once an object was put in front of the impedance controller that forced it into a singularity position, its pwm signals became saturated very quickly leading to the motors moving in an extremely jerky manner at the output end and passing through some of that noise back to the leader arm.

![PWM Signals Impedance](/assets/images/teleop/impedance_pwm.png){: width="90%"}

*Figure: Saturated PWM Signals on Follower Joints Under Impedance Control. Spikes Occur at Singularity Joint Angles*

<!-- <video width="100%" controls> -->
<video width="100%" autoplay muted loop playsinline>
  <source src="{{ '/assets/images/teleop/Contact-Impedance.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>

### Comparative Success: Gain Scheduling
In contrast, our **Gain Scheduling** algorithm proved more robust for real-world obstacle interaction. By incrementing the proportional gain of the leader arm linearly ($K_{P_{L}} = K_{P_{L}} \times (1 + 0.2 \times iterations)$) when tracking error persisted, it effectively "slowed down" the user's hand during collisions. This provided a much more reliable haptic experience than the impedance controller's current iteration.

![Position Error Graph](/assets/images/teleop/error_plots_pd.png){: width="90%"}

*Figure: Position Error for PD control and PD control with gain scheduling*

![Position Error Graph](/assets/images/teleop/pdgs_pwm.png){: width="90%"}

*Figure: PWM Signals for PD control with gain scheduling. PWM Spikes correlate with follower arm contact with object*

<video width="100%" autoplay muted loop playsinline>
  <source src="{{ '/assets/images/teleop/Contact-PD-Gain-Scheduling.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>


## Challenges and Lessons Learned

The greatest hurdle we faced was **latency**; motor read/write commands introduced over 40ms of delay per iteration, capping our control frequency at 25 Hz. In future iterations, we would prioritize:
* **Higher Frequency Control:** Using a dedicated microcontroller like a Teensy to reduce loop times.
* **Advanced Filtering:** Replacing the moving average with a more robust low-pass filter to reduce noise without the same level of delay.
* **Singularity Management:** Developing a "controller hand-off" strategy to prevent the arm from entering singular configurations.

***

### Team Members
* **Jed Langer Weida** (UCLA): Arm CAD, assembly, and controller design.
* **Harry Sandstrom** (UCLA): Impedance control code and gain tuning.
* **Dylan Santora** (UCLA): Decentralized PD and Gain Scheduling design.
* **Alexander Tsao** (UCLA): Impedance design and PD gain tuning.

### References
* [1] Hirzinger, G., et al. (2005). "Rokviss-robotics component verification on ISS".
* [2] Kron, A., et al. (2004). "Disposal of explosive ordnances by use of a bimanual haptic telepresence system".
* [3] Bruno Siciliano, et al. (2010). "Robotics: Modelling, Planning and Control".
