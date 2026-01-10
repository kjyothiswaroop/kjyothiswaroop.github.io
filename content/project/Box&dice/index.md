---
type: project
title: Jack in a Box
summary: Lagrangian Dynamics and Impacts with a jack in a box.
tags:
  - Python
  - Sympy
  - Dynamics
  - Simulation

date: 2026-01-03
weight: 4
share: false
links:
  - name: Code
    url: https://github.com/kjyothiswaroop/JackinBox
    icon: github
    icon_pack: fab
# image: "https://www.youtube.com/watch?v=KEsO1CyqTHA"
project_title: Dynamics and Impacts of a 2-D Jack and Box system
author: Jyothi Swaroop
video: https://github.com/user-attachments/assets/d0c4b964-98b2-45b2-a35a-5f40bdab1a21
---

---------------------------------------------------------------------
## Overview
This project models the dynamics and impacts of a **Jack in a hollow box** system floating without gravity. The jack(*blue square*) is given an initial velocity in the x-direction and the box(*red square*) is given a rotational velocity along its *z-axis*. All the impacts are assumed to be **elastic** impacts(i.e. energy is not lost as a result of the collision.)

The dynamics and impacts are modelled using the **Lagrangian Dynamics** specifically the *Euler-Lagrange* equations.

---------------------------------------------------------------------
## Setup

The state of the system has 6 variables they are : 

$$q = (x_{jack}, y_{jack}, \theta_{jack},x_{box}, y_{box}, \theta_{box})$$

The steps to taken to model the system :
* Setup Euler-Lagrange Equations.
* Impact equations setup.
* Impact updates.
* Plotly Animation.

All of the programming was done using the python library `sympy`

---------------------------------------------------------------------
## Equations

### Lagrangian Formulation
For any dynamics system , the Lagrangian of the system is defined as :

$$ L = T - V $$

where
  - T : Total Kinetic Energy of the system.
  - V : Total Potential Energy of the system.

### Euler-Lagrange Equation

The Euler-Lagrange equations are used to solve for the dynamics of the system(i.e. $q , \dot{q}$)

$
\frac{d}{dt}\left(\frac{\partial L}{\partial \dot{q}}\right) - \frac{\partial L}{\partial q} = F
$

The resulting expressions for $\ddot{q}$ are solved symbolically and lambdified into fast numerical functions for time integration.

### Constraint Equations
The constraints for the jack are the equations which should represent that the box cannot move out of the box.

For the constraints:
- Four corners of the jack
- Four edges of the box

It is considered that only one collision can occur at a time and it will be point collision and not a surface to surface collision.

### Simulation Loop

The simulator uses fixed-step RK4 for continuous dynamics. At each step:

1. Evaluate constraint values $\phi_k $ 
2. Detect an impending impact if $\phi_k $is near contact and decreasing  
3. If impact occurs, apply an impulsive update $\dot{q}^- \to \dot{q}^+ $ 
4. Continue integration with updated velocities  

This produces piecewise-smooth trajectories with discrete jump events.

### Acknowledgements
This project was developed as a final project for the **ME-314 Theory of Machines : Dynamics** taught by **Professor Todd Murphey**. 