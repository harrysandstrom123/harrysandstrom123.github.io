---
title: "Twostage Ignition Rocket"
date: 2023-06-30
categories:
  - blog
tags:
  - Projects
  - Undergrad
---

This project was a collaboration with my groupmates and I, for our undergraduate capstone, to build a rocket to carry an egg to the highest altitude and return it unharmed.

The entire project consisted of designing the entire rocket from scratch with the option to build a single stage rocket, one that had two stages, or a hybrid rocket. Our team opted to build a two stage rocket for the class in order to increase the complexity of our electronics and our understanding.

For the creation of our two stage design we opted to go with electronic ignition for the second stage due to its higher reliability and extra safey factor in case of a launch that did not go vertically of the pad.

I was designated the lead on the design of the electronics package and ignition system, so I will focus on its design and performance here.

The electronic ignition system required the timed triggering of the second stage motor at a set time after launch. To do this we created the following circuit that would light the e-match in the second stage when the flight computer (a teensy 4.0) triggered the ignition signal.

![Ignition Diagram Photo](/assets/images/ignition_diagram.png){: width="50%"}

![Ignition System Photo](/assets/images/ignition_system.png)

![launchpad Photo](/assets/images/launchpad.png)

![Assembled Photo](/assets/images/ready_for_launch.png)

![Rocket Apogee](/assets/images/rocket_apogee.png)
