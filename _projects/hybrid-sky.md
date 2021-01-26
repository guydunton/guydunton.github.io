---
layout: default
title: Hybrid Sky
permalink: /projects/hybrid-sky/
excerpt: An arcade air combat game built as a group project at university. Also built using C# & the XNA framework
order: 1
---

## Hybrid Sky

Hybrid Sky is a game created as a group based university project. It is an arcade style combat flight game set in a procedurally generated abstract world. Inspired by the artwork of mirrors edge and the mechanics of the transformers games. The combat system revolves around players being able to switch on the fly between a tank like helicopter and a nimble but vulnerable jet. Written in C# and HSLS. (If you watch the video sorry but the audio was mandatory :p ). My role on the project was as lead programmer meaning that I designed a large portion of the code in the game as well as implementing some of the subsystems.

**My contributions**

- Designing the class layout of a large portion of the game, including, levels and loading, assets, the player, the rendering system and the particle systems.
- I wrote a HLSL rendering system that included both a deferred lighting solution and a colour bloom. This system also had to be insulated to allow for two screen to be side by side to allow for split screen play.
- I wrote the procedural asset creation code. The floating islands and all the box buildings are created at load time. The islands are created using mathematical rules that place all the points in world space along geometric lines, but also set all the indexes needed to render all the points as triangles on screen.
- I created the code for the player model to be controlled and for the duel flight modes. This involved a lot of finding controls that 'felt right' and creating rules for the fixed wing flight and a vtol flight model. The fixed wing used rules based partially on real world physics to give the player lift and thrust while the vtol mode used more abstract methods to simulate helicopter flight, such as alternative pivot points for rotation and a vertical boost to give thrust.
- I designed and wrote a large portion of the particle systems used to indicate player damage and the waterfall effects. This system was created with ease of use and customizability in mind so that other programmers on the team would find it easy to create particle effects and place them at events or in the world, without having to change the core of the particle system code.

<a href="https://youtu.be/wBsGQinzkog" target="_blank">
![Youtube video of Hybrid sky](/assets/youtube/hybrid_sky.png)
</a>

Another big thanks to Tom, Pete and Mike, with whom I had enormous fun making this game.
