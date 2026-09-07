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

# Framerate

<img width="605" height="371" alt="50kboids" src="https://github.com/user-attachments/assets/586df525-d1d2-45c7-8082-1fd0400ff4db" />

<img width="605" height="371" alt="100kboids" src="https://github.com/user-attachments/assets/bffc856b-7b12-463f-b5ef-a4421c45877d" />

- Framerate efficiency is best with the coherent grid, followed by the scattered grid, followed by the naive implementation.
- All framerates on average dropped due to the increase from 50k boids to 100k boids.


