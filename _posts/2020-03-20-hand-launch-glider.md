---
title: "#Modelling the Skies: Comparing Theoretical and Experimental Glider Flight Paths"
date: 2025-12-03
excerpt_separator: "<!--more-->"
use_math: true
categories:
  - blog
tags:
  - Projects
  - Undergraduate
---

# This article is currently a work in progress.
# Modelling the Skies: Comparing Theoretical and Experimental Glider Flight Paths

Predicting the trajectory of an aircraft is essential for applications ranging from hobbyist soaring to humanitarian aid delivery. Our experiment sought to determine if a simple glider’s flight path could be accurately modeled using general aircraft formulas for lift and drag under ideal scenarios.

## Experimental Design and Methodology

To test our theoretical model, we constructed a simple glider with a $1.4$ m wingspan and a total mass of $440$ grams. The aircraft was built from accessible materials, including foam core poster board and wooden dowels. We utilized a KF-step airfoil for the wings, a design favored by hobbyists for its simplicity and effectiveness.

![Plane Picture](/assets/images/glider/airplane_design.png){: width="90%"}


**Our data collection system relied on**:

- **Onboard Instrumentation**: An Arduino unit equipped with an accelerometer was duct-taped to the front of the fuselage to log real-time acceleration data.

![accelerometer data](/assets/images/glider/yz_acc.png){: width="90%"}

- **Visual Analysis**: We filmed launches against wall markers to determine initial velocity and launch height.

- **Computational Modeling**: We used Ansys Fluent to perform flow analysis on a 2D model of our airfoil. This yielded a coefficient of drag $(C_d)$ of $0.079$ and a coefficient of lift $(C_l)$ of $0.1684$.

![CFD](/assets/images/glider/airfoil_cfd.png){: width="90%"}

## Comparative Analysis

We conducted 20 trials, with the most successful flights reaching distances of approximately $9.10$ m. Using a trapezoidal sum to integrate the accelerometer's acceleration data into velocity and position, we compared the physical results against a theoretical model adapted from the University of Connecticut.

- **Experimental Accuracy**: The data integrated from the onboard accelerometer came within 7.6% of the actual distance flown.

- **Model Accuracy**: The theoretical model was slightly less precise, with a 14.7% error compared to the recorded distance.


![Plane Picture](/assets/images/glider/airplane_flight_dist_v_model.png){: width="90%"}

**Challenges and Future Improvements**

Several sources of error impacted our results, most notably the limitations of our instrumentation and human factors. The video analysis was hindered by downscaled framerates, making initial velocity difficult to measure precisely. Furthermore, our accelerometer maxed out at 2 g’s during launch, potentially throwing off calibration for the remainder of the flight.

**To refine future predictions, we recommend**:

- **Gyroscopic Sensors**: Utilizing a gyroscopic accelerometer would correct for changes in the glider's inclination, providing more accurate horizontal and vertical readings.

- **Mechanical Launching**: A dedicated launch contraption would ensure a consistent initial angle $(θ)$, reducing human variability.

- **High-Speed Videography**: Using high-resolution, high-framerate cameras would allow for superior tracking of the entire flight path.
