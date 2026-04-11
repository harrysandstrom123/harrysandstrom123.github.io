---
title: "Twostage Ignition Rocket"
date: 2023-06-30
categories:
  - blog
tags:
  - Projects
  - Undergrad
---

# Project Overview: Dual-Stage High-Altitude Rocketry

For our undergraduate capstone, my team and I designed and manufactured a high-power rocket tasked with carrying a fragile payload (an egg) to maximum altitude and returning it safely. While the project allowed for single-stage or hybrid designs, we chose to develop a two-stage rocket to challenge ourselves with increased system complexity and advanced avionics integration.
Ignition Strategy & Safety

We implemented an electronic ignition system for the second stage. This method was selected over mechanical alternatives because it offered superior reliability and an added safety margin; specifically, the flight computer could inhibit ignition if the rocket’s ascent angle deviated from a safe vertical trajectory.
Avionics & Circuit Design

As the Electronics and Ignition Lead, I headed the design and performance validation of the flight package. The core requirement was the precision-timed triggering of the second-stage motor that incorporated safety features, such as no possibility of pre-launch ignition, nor ignition if the rocket did not reach above a threshold altitude upon launch.

## How the System Functions:
To ensure successful ignition, I designed a Capacitor Discharge Circuit (CDC) controlled by a Teensy 4.0 flight computer:

* Energy Storage: The system first pulls power from the onboard battery to charge a high-capacity capacitor. This ensures a high-current burst is available to reliably fire the e-match, regardless of battery fluctuations.

* Trigger Logic: At a pre-programmed interval post-launch, the Teensy 4.0 pulls a digital output pin HIGH. This logic could only occur if a wire acting as a short between two pins on the teensy was removed, so it was used as the launch detection trigger in tandem with an altitude reading to ensure that the vehicle had both left the pad and reached a preset minimum altitude after launch before the ignition could be triggered.

* Switching: This signal activates a transistor (acting as a switch), allowing the stored energy in the capacitor to discharge through the e-match, successfully igniting the second-stage motor.

![Ignition Diagram Photo](/assets/images/ignition_diagram.png){: width="90%"}

![Ignition System Photo](/assets/images/ignition_system.png){: width="90%"}

## Assembled Rocket and Flight

Once all of our flight ignition systems had passed multiple checks and met all criteria we were ready to head to the desert and launch!

![Assembled Photo](/assets/images/ready_for_launch.png){: width="90%"}

![launchpad Photo](/assets/images/launchpad.png){: width="90%"}

Upon recovery of the rocket when we removed the nose cone we saw smoke, which likely came from the second stage ejection charge burning the “recovery wadding” (mostly tissue paper). However we removed the burning parts and everything seemed to be alright inside aside from a slight melt of the camera. We recovered all parts of the rocket including the first stage, which ejected correctly as the second stage ignited.

When comparing our flight trajectory to our simulation our actual apogee of 155 m compared to our prediction of 434m, a deviation of -65% from our prediction. This error can be accounted for by additional weight that was added to our rocket on launch day in the form of our wadding and last minute additions of glue and duct tape to put the launch lugs and re-secure the fins, as well as a higher drag coefficient that we were unable to record. Additionally, our second stage, upon ignition, spiraled, causing its trajectory to become more horizontal and dump a large portion of the vertical velocity that we would have added with the second stage. This can be seen with our peak velocity being 71 m/s as opposed to 106 m/s as projected. We can see in figures 24 and 25 that, while our actual trajectory doesn’t quite follow the simulation, the key timings of the second motor ignition and peak velocity line up fairly well, showing that with some more refinement of our rocket to eliminate the spiral and reduce weight could potentially achieve the predictions made by our simulation. While this number was quite a ways off, we actually had the most accurate altitude prediction out of any team, due to most simulations being very optimistic by the other teams compared to our much more conservative flight model.

![Rocket Apogee](/assets/images/rocket_apogee.png){: width="90%"}

Special thanks to my teammates who helped make this flight happen.

![TA Photo](/assets/images/ta_photo.jpg){: width="90%"}


