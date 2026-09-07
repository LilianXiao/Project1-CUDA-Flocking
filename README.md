**University of Pennsylvania, CIS 5650: GPU Programming and Architecture,
Project 1 - Flocking**

* Lilian Xiao
  * [LinkedIn](https://www.linkedin.com/in/lilian-xiao/), [personal website](https://lilianxiao.carrd.co/), [art website](https://lilianxvis.carrd.co/)
* Tested on: Windows 11, Intel(R) Core(TM) Ultra 9 185H (2.50 GHz), 16.0 GB RAM, NVIDIA GeForce RTX 4070 Laptop GPU (8 GB)
Intel(R) Arc(TM) Graphics (128 MB) (Personal Laptop)

### Boids Flocking CUDA Simulation

https://github.com/user-attachments/assets/a46abf71-ce76-4cfe-a586-34a9581f93f4

https://github.com/user-attachments/assets/e6e40d27-43d3-4630-9217-8fe330653b46

This simulation involves three different approaches: **Naive**, **Scattered**, and **Coherent**.  The first implementation involves naively checking each boid against every other boid, following the three rules of flocking (cohesion, separation, and alignment).

- **Cohesion**: A boid moves to the average position of neighboring boids.
- **Separation**: A boid will not collide with neighboring boids within the separation distance.
- **Alignment**: A boid will try and match the velocity of neighboring boids.

The other two implementations are much better optimized, using a **uniform grid** that only permits boids in some neighborhood distance to affect each other.  While the second implementation only preserves boid cell memory contiguousness, the third implementation ensures that velocities and positions of boids per cell are contiguous.

### Performance Analysis

# Framerate among different simulation methods

<img width="605" height="371" alt="50kboids" src="https://github.com/user-attachments/assets/586df525-d1d2-45c7-8082-1fd0400ff4db" />

<img width="603" height="370" alt="50kboidsvis" src="https://github.com/user-attachments/assets/a3b16f26-735d-4338-ba2f-ebfc1926a5d5" />

<img width="605" height="371" alt="100kboids" src="https://github.com/user-attachments/assets/bffc856b-7b12-463f-b5ef-a4421c45877d" />

With 50k boids, framerates were on average: 85.8, 440.3, and 1634.7 FPS for each simulation method respectively.

With 50k boids and visualization on, framerates were on average: 56.9,	405.9,	and 767.7 FPS for each simulation method respectively.

With 100k boids, framerates were on average: 18.85 216.0, and 1209.5 FPS for each simulation method respectively.

- Framerate efficiency is best with the coherent grid, followed by the scattered grid, followed by the naive implementation.
- All framerates on average dropped due to the increase from 50k boids to 100k boids.
- For a very large number of boids, efficiency is clearly the best with grid-optimized methods.
- For a considerably small number of boids (~1000), all three methods had roughly the same framerate changes (ranging from ~800 to ~1100).  The difference in optimization is negligible when it comes to a small-enough number of objects, and thus there is no discernable difference between grid and brute-force methods.
- Comparing the 50k boid simulations with and without visualization, framerates across all three were consistently worse on average when visualization was on.

There is an undeniable improvement when using the coherent uniform grid over the scattered uniform grid and naive methods.
- There is O(n) work per-boid naively, due to checking every other boid.  However, there is O(b) work done in both uniform grid implementations, where b is some number of boids in the neighborhood block, and we assume non-worst case that b < n.
- Overall, the naive implementation ends up doing O(n^2) work.  However, for the uniform grid implementations, this can be reduced to roughly O(n).
- Specifically, the scattered uniform grid has slightly worse memory access than the coherent uniform grid due to the usage of the indirect particleArrayIndices.  This is mediated in the coherent version by directly using the position data and making both position and velocity data contiguous in memory, so they are more easily accessed.

# Framerate change: variable block size

<img width="603" height="370" alt="framerate_blocksize" src="https://github.com/user-attachments/assets/2d002ffb-b679-4845-8b4f-06abb5df3727" />

Actually, framerate isn't consistently affected by block size variability.  That is, there is no cohesive observable improvement in framerate among raised or lowered block size analysis.  Importantly, the work being done is mostly affected by memory, whereas block-size affects occupancy.  An improved block size could mediate latency, but this may not have an effect on memory optimization.  This can be especially noteworthy when it comes to using uniform grid methodology.

It is worth noting that the maximum block size tested (1024) is consistently slower than the others.  This is interesting, and can possibly be explained by the GPU limiting the blocks that can reside on an SM concurrently.

# Framerate change: cell width/27 cell vs. 8 cell

<img width="605" height="371" alt="27v8" src="https://github.com/user-attachments/assets/f2b1ebad-b55b-4ea1-9288-d8f64206c9b0" />

One can observe that the 8 cells results in a relatively higher average framerate simulation than with 27 cells, which goes along with my expectations.

- Logically, the 8 cell implementation will not check more than the 27 cell implementation, and therefore will never do more work.  This results in slightly more efficient lookup, and reduces 19 extra cells from the picture.
- Unlike testing framerate using variable block size, this has a notably observable difference, as we have reduced the density of boids.  Thus, one can probably expect the efficiency difference to become even greater as the difference increases between 27 vs. 8 cells.
