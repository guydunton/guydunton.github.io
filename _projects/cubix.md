---
layout: default
title: Cubix
permalink: /projects/cubix/
excerpt: An interactive art piece, displaying my course logo
order: 2
---

## Cubix

An interactive exhibit piece created during university, Cubix was written in C# using the XNA framework and features and interactive logo which lights up and reacts following mouse or stylus input. This project features a deferred shading technique to simulate lights all through the scene and uses a spring like physics system to compute the motion following user input.

- Written using custom HLSL code to produce a deferred lighting setup which allows the placing of a point light in each cube in the grid. The intensity of the light is determined by the motion of the attached cube. Certain cubes will remain lit after being struck to create a logo.
- Physics code was written to simulate spring like forces between all the cubes creating a pseudo cloth simulation.
- Audio code monitors the total motion of all the cubes in order to calculate the volumes of multiple chime tracks.
- A simple script state machine operates to determine when the grid has been completed. It then passes a wave through the grid allowing the lit cubes to be turned off and the input to start again.

<iframe width="560" height="315" src="https://www.youtube.com/embed/J7V02FZv3tA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

A huge thanks to Tom, Pete and Mike for making this project with me.
