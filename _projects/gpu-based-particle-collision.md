---
layout: default
title: GPU Based Particle Collision
permalink: /projects/gpu-based-particle/
excerpt: My dissertation project; simulating collisions between particles and geometry on the GPU
order: 3
---

## GPU Based Particle Collision

The subject of my university dissertation was GPU based particle system collision. GPU based particle systems are able to simulate many thousands of particles while being unable to interact with the environment. More traditional particle systems are run on the CPU, can interact with environment objects but are unable to simulate the large numbers of particles that the GPU based solution can. The goal was to explore the viability of a GPU based particle system that was required to interact with scene elements. This included, how easy it was to write the code, the quality of the interactions and the performance of the two simulations. The project was written in C++, utilising DirectX 10 and large amounts of shader code. The program itself contained a GPU based particle system and a CPU based particle system for comparison.

- Features custom HLSL code which renders world geometry into a format that can be interpreted by the GPU during the particle update step.
- Updating GPU particles is done on the GPU and makes use of the encoded scene data in when updating the particles. HLSL code reinterprets the scene data into a series of planes against which particle collision is calculated. The GPU then runs some simple physics calculations on each particle to create realistic movement.
- Also includes a particle system implemented in more conventional C++ code to test performance against the GPU implementation. This CPU implementation features instancing to reduce the render time of all the particles in order to make the comparison fairer.
- The program runs with an uncapped framerate, allowing the performance of both systems to be judged based on their maximum frames per second.

<a href="https://youtu.be/NnJ7KibU8MA" target="_blank">
![Youtube video of gpu particles](/assets/youtube/gpu_particles.png)
</a>
