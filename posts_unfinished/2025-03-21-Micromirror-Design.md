---
title: "Micro Mirror Design"
date: 2026-03-21
excerpt_separator: "<!--more-->"
use_math: true
categories:
  - blog
tags:
  - Projects
  - Graduate
---

Single DOF Actuated Piston Micro-Mirror
Project Overview

Micro-mirror arrays are essential for precision optical applications, including LIDAR, optical communication, and wavefront shaping. While most market options focus on tip-tilt-piston motion, this project focuses on the design of a pistoning-only micro-mirror optimized for high-speed phase modulation. By utilizing the FACT (Freedom and Constraint Topologies) methodology, I developed a symmetric stacked blade flexure that maximizes axial compliance while suppressing off-axis resonant modes.
Design & Topology

To achieve a pure 1-DOF translational motion, I employed a topology consisting of stacked parallel planes with an order of constraint of 5.

    Symmetry: The blade pattern is mirrored across the central vertical symmetry line to eliminate parasitic error. This results in a thermally stable system with an increased order of constraint of 12.

    Space Optimization: Rather than attaching to the mirror sides, blades are connected to a central wall protruding downwards. This maximizes the reflective surface area relative to the total device footprint.

    Mass Reduction: The central wall design reduces the stage mass, which is critical for increasing the natural frequency of the device.

Resonant Frequency Analysis

The performance was validated using two primary methods: a custom Euler stiffness matrix Matlab program and FEA modal analysis. The design utilizes <110> Silicon for its superior properties in flexure elements.
Mode	Euler Stiffness (Matlab)	FEA Modal Analysis
Mode 1 (Pistoning)	

25.030 kHz
	

28.7 kHz
Mode 2 (Tilting)	

374.486 kHz
	

307.052 kHz

The analysis confirmed that secondary resonant modes occur at frequencies more than one order of magnitude higher than the primary pistoning mode, ensuring stable operation without unwanted tilting.
Fabrication Strategy

The proposed device is manufactured via surface micromachining, a process that builds the topology through stacked thin films.

    Device Layer: Selection of a 15µm device layer and 1-2µm buried silicon oxide for etch stop and mechanical isolation.

    Lithography & Etching: Use of Deep Reactive Ion Etching (DRIE) to pattern individual flexure blades.

    Layering: Void filling with sacrificial oxide to repeat the process for multiple blade layers.

    Release: Final deposition of the mirror surface followed by the removal of silicon dioxide to free the moving components.

Technical Specifications

    Mirror Dimensions: 1mm x 1mm 

    Blade Thickness: 15µm 

    Primary Actuation: Resonant actuation via Comb Drive Actuator 

    Target Frequency: ~25-28 kHz 

Are you interested in seeing the specific Matlab code used to generate the Euler stiffness matrix for this project?

can you output it as a markdown file without the links to the citations

    Query successful

Your Markdown file is ready. I have also included a PDF and HTML version of the page layout for your records.
MD icon
 piston_mic...or_project 
MD
PDF icon
 piston_mic..._portfolio 
PDF
HTML icon
 piston_mic...irror_page 
HTML


Gemini is AI and can make mistakes.

# Single DOF Actuated Piston Micro-Mirror

### Project Overview
Micro-mirror arrays are essential for precision optical applications, including **LIDAR**, **optical communication**, and **wavefront shaping**. While most market options focus on tip-tilt-piston motion, this project focuses on the design of a **pistoning-only micro-mirror** optimized for high-speed phase modulation. By utilizing the **FACT (Freedom and Constraint Topologies) methodology**, I developed a symmetric stacked blade flexure that maximizes axial compliance while suppressing off-axis resonant modes.



---

### Design & Topology
To achieve a pure 1-DOF translational motion, I employed a topology consisting of **stacked parallel planes** with an order of constraint of 5. 

![Stacked Planes](/assets/images/micro_mirror/CompliantTheoryPic.png){: width="90%"}

* **Symmetry**: The blade pattern is mirrored across the central vertical symmetry line to eliminate **parasitic error**. This results in a thermally stable system with an increased order of constraint of 12.
* **Space Optimization**: Rather than attaching to the mirror sides, blades are connected to a **central wall** protruding downwards. This maximizes the reflective surface area relative to the total device footprint.
* **Mass Reduction**: The central wall design reduces the stage mass, which is critical for increasing the natural frequency of the device.

![Mirror Design](/assets/images/micro_mirror/Micromirror%20Design.png){: width="90%"}



---

### Resonant Frequency Analysis
The performance was validated using two primary methods: a custom **Euler stiffness matrix** Matlab program and **FEA modal analysis**. The design utilizes **<110> Silicon** for its superior properties in flexure elements.

| Mode | Euler Stiffness (Matlab) | FEA Modal Analysis |
| :--- | :--- | :--- |
| **Mode 1 (Pistoning)** | 25.030 kHz | 28.7 kHz |
| **Mode 2 (Tilting)** | 374.486 kHz | 307.052 kHz |

![Mode 1](/assets/images/micro_mirror/Frequency_Analysis_m1.png){: width="90%"}
*Mode 1 - Pistoning Modal Analysis (28.7 kHz) in SolidWorks*

![Mode 2](/assets/images/micro_mirror/Frequency_Analysis_m2.png){: width="90%"}
*Mode 2 - Twisting Modal Analysis (307.052 kHz) in SolidWorks*

The analysis confirmed that secondary resonant modes occur at frequencies more than **one order of magnitude higher** than the primary pistoning mode, ensuring stable operation without unwanted tilting.


---

### Fabrication Strategy
The proposed device is manufactured via **surface micromachining**, a process that builds the topology through stacked thin films.

1.  **Device Layer**: Selection of a 15µm device layer and 1-2µm buried silicon oxide for etch stop and mechanical isolation.
2.  **Lithography & Etching**: Use of **Deep Reactive Ion Etching (DRIE)** to pattern individual flexure blades.
3.  **Layering**: Void filling with sacrificial oxide to repeat the process for multiple blade layers.
4.  **Release**: Final deposition of the mirror surface followed by the removal of silicon dioxide to free the moving components.

---

### Technical Specifications
* **Mirror Dimensions**: 1mm x 1mm
* **Blade Thickness**: 15µm
* **Primary Actuation**: Resonant actuation via **Comb Drive Actuator**
* **Target Frequency**: ~25-28 kHz
