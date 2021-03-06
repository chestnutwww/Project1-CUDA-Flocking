**University of Pennsylvania, CIS 565: GPU Programming and Architecture,
Project 1 - Flocking**

* Yalun Hu
* Tested on: Windows 10, i7-6700HQ CPU @ 2.60GHz 32GB, GTX 1070 8192MB (Personal computer)

# Flocking

![](images/flock.gif)

# Features

* Naive Boids Simulation
* Scattered uniform grids Boids Simulation
* Coherent uniform grids Boids Simulation

# Performance Analysis

## Methods for measuring performance

**Framerate change:**
In main.cpp, add variables and loops to record fps in first 1000 calls of mainLoop( ) function, and average them to get the average fps.
To unlock the fps limitation, I turned off vertical synchronization in NVIDIA control panel.


**Time of step simulation:**
In main.cpp, add variables and loops to record step simulation time in first 1000 calls of runCUDA( ) function, and average them to get the average step simulation time.
The elapse time of step simulation is computed by cudaEvent.

## Performance plots

### 1. Framerate change with increasing # of boids

* with visualization

<p align="center">
  <img src="images/fps-boids-visual.png">
</p>

* without visualization

<p align="center">
  <img src="images/fps-boids-nonvisual.png">
</p>

These 2 figures show that the fps decreases as the # of boids grows. The Naive method has higher fps than the rest two methods, but as the # of boids grows, the coherent method has the best performance.

### 2. Framerate change with increasing block size

<p align="center">
  <img src="images/fps-block-nonvisual.png">
</p>

It seems that the increasing of block size does not affect fps too much.

### 3. Step simulation time with increasing # of boids

* with visualization

<p align="center">
  <img src="images/time-boids-visual.png">
</p>

* without visualization

<p align="center">
  <img src="images/time-boids-nonvisual.png">
</p>

These 2 figures show that the  step simulation time grows as the # of boids grows. The Naive method become much more slower when the # of boids is big enough. And the advantage of the coherent method is more obvious for large # of boids.

### 4. Step simulation time with increasing block size

<p align="center">
  <img src="images/time-block-nonvisual.png">
</p>

It seems that the increasing of block size does not affect step simulation time too much.

# Questions and answers

**1. For each implementation, how does changing the number of boids affect performance? Why do you think this is?**

The increasing of the number of boids leads to the decreasing of performance, since there are more neighbors needed to be check.

**2. For each implementation, how does changing the block count and block size affect performance? Why do you think this is?**

Changing the block count and block size does not affect performance.

**3. For the coherent uniform grid: did you experience any performance improvements with the more coherent uniform grid? Was this the outcome you expected? Why or why not?**

There are performance improvements in fps and runtime when the number of boids is big. But when the number of boids is small, the coherent uniform grid method does not improve the performance.

I think it's because when the number of boids is big, neighborhood distance is much smaller than the full simulation space, which means having each boid check every other boid is very inefficient.

When the number of boids is small, the neighborhood distance is not such small, and the code length of the coherent uniform grid is longer than naive method, so the naive method has a better performance.

**4. Did changing cell width and checking 27 vs 8 neighboring cells affect performance? Why or why not?**

Checking 27 neighboring cells will improve the performance, and the performance increases much more as the number of boids becomes bigger.

When checking 27 neighboring cells, the width of each cell is the neighboring radius(R), and the whole volume of the 27 cells is 3 * 3 * 3 * (R^3) = 27 * (R^3).

When checking 8 neighboring cells, the width of each cell is 2 times the neighboring radius, and the whole volume of the 8 cells is 4 * 4 * 4 * (R^3) = 64 * (R^3).

So checking 27 neighboring cells has smaller volume and less number of boids to check.
