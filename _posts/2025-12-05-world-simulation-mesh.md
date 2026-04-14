---
title: "Elastic World Simulation Model"
date: 2025-12-03
excerpt_separator: "<!--more-->"
use_math: true
categories:
  - blog
tags:
  - Projects
  - Graduate
---

# This article is a work in progress


# Elastic World Simulation Model

For the final project of the class MAE 263F Flexible Robotics at ucla. I created a simulation model based off of a [spandex gravity well model][orbital link].

The model is simulated through the creation of a mesh that has $N\timesN$ nodes. These nodes are connected through two types of structures to each other. Firstly, each node is connected to its nearest neighbors by edges which are treated as springs. Secondly, each set of triangular nodes bends with an adjacent triangle as a hinge. These two features allow this nodal mesh to simulate the elasticity and bending stiffness of a solid body, with the stiffnesses of the springs and hinges tuned to match.

![Initial Mesh Setup](/assets/images/Mesh_initial.svg){: width="90%"}

The system operates through the use of an Implicit Euler solver using the Newton-Raphson method for stability. Every timestep in the simulation uses an objective function to minimize error on the system energy + inertial potentials.

Due to the introduction of the balls simulating the planets I had to implement a simple contact model that would apply the force of a ball over the surface that was proportional to the ball's penetration through the surface.


![Orbiting Diagram](/assets/images/multi_ball_impact_rolling.gif){: width="90%"}

[orbital link]: https://www.youtube.com/watch?v=Pdq78yXiyR0
