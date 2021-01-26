---
layout: default
title: Asteroids Redux
permalink: /projects/asteroids-redux/
excerpt: A remake of Asteroids, written using C++ & DirectX 11. Featuring more advanced collisions and physics than the original
img_src: /assets/projects/asteroids.png
img_alt: Asteroids Redux running on Windows
order: 4
---

## Asteroids Redux

Asteroids Redux is a remake of the Atari classic game Asteroids. Written in C++ and using DirectX. <a href="https://github.com/gdunton/asteroids-redux">Code available here</a>

<a href="https://youtu.be/qw1Vv8KV5q8" target="_blank">
![Youtube video of Asteroids redux](/assets/youtube/asteroids.png)
</a>

### Original

Originally Asteroids Redux was written around 2014 using C++11 and DirectX 9. Almost all of the code was written from scratch (excluding a couple of libraries) and it features a few alterations over the original game.

#### World Wrapping

In the original Asteroids, objects would dissappear completely off the side of the screen before appearing again on the opposite side of the screen. In Redux I added custom rendering so that objects would be visible on both sides of the screen at the same time.

A 3x3 matrix of cameras was used to render objects. When an Asteroids (or player) reaches the edge of the screen they are rendered by the main central camera but also by the camera at that edge. This overlays the object on the screen multiple times and gives the illusion that the object is in both places at once.

#### Rigid Body Collisions

Asteroids Redux features a rigid body collision system, meaning that asteroids will bounce off each other as well as the player.

In order to prevent slowdown when large numbers of asteroids are on screen collision detection is optimized using a <a href="https://en.wikipedia.org/wiki/Quadtree">quad tree</a>. This optimization means that the sreen needs to be full of asteroids before a slowdown due to collision detection is detected.

<hr/>

### Rewrite

In 2018 I was migrating my website and decided to check out the Asteroids code which I had been using as part of my portfolio. I was not impressed. Rather than lose the code altogether I decided that I would refactor the code so that it was more representative of my current coding style and it followed best practices.

_Refactor vs rewrite_
Note that I chose to refactor the code rather than throw it away and start again. I believe that attempting to rewrite the code would have taken a lot more time to do and would have left me fighting with a lot of the same bugs that I fixed in the original. I believe that I would never have actually finished a rewrite, whereas I could finish a refactor at any time because I kept the code working the whole time.

These are the bad practices I noticed as I went through the code, as well as how I removed/fixed:

- Singletons & Managers- It seams I really liked singletons at one time. Removing all the hidden dependencies on singletones was a tough job but made all dependencies clearer.
- Raw Pointers- Passing raw pointers left a lot of spots that would break at any code change. The QuadTree class in particular used raw pointers to keep track of objects and changing this to smart pointers fixed a lot of use-after-delete bugs.
- Initialize Functions- I have changed a lot of the classes away from 2 step initialization. There just isn't really a need for it and objects shouldn't be possible to use before they are correctly initialized.
- Ignoring Language/Standard Library- I had an additional dependency on a library called "fast delegate" which I completely removed in favour of lambdas and std::function.

### Conclusion

The code is still not completely refactored but at least it contains fewer obvious mistakes and I'm not so ashamed to show the code to other people.
