---
tags: ["projects", "simulation"]
title: E-Puck Wall Following Project
summary: A e-puck robot simulated in Webots that solves a maze using wall following techniques
date: 2024-05-06
---

### Finding the shortest path outside a maze following a wall

## How it works

-The robot **must always** begin from the left side (the left wall).

-The start line and the finish line are physically denoted by **two small black tapes** on the floor.

-The robot has four phases:

  1. Following the left wall from the **start** towards the **finish** line.
  2. Once arrived at the finish line, it stores the distance from the start line to the finish using **left wall**. It then continues its run along the same left wall.
  3. At the start line once again in stores the distance of the **right wall**.
  4. A last run is made following the **shortest wall** found towards the finish line.

## Structure of the code

  The code is modularized in 4 different modules that are connected through an header file with the main source as shown below
  
  ![Screenshot from 2024-01-04 14-14-56](https://github.com/Gandalf789/pathfinder-robot/assets/109030213/5e78930c-0322-4aa9-a774-ccc089953cfc)

#### P.S.
The code can be found on my [github](https://github.com/ciupei-timotei-30323/pathfinder-robot)
