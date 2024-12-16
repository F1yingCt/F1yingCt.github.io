---
layout: mypost
title: VR Skeleton Simulator
categories: [Research]
date: 2024-2-25
pinned: false
---

Luge, bobsled, and skeleton are collectively known as winter sliding or sledding sports. These sports are generally similar in form and performed on a U-shaped ice track that twists along mountainsides. 

Skeleton is the fastest and most dangerous sport among the three sliding sports, and athletes are constrained to slide three times per day. It is helpful for athletes to be au fait with the track before their training and slide more times each day. Thus, our project established a VR skeleton simulator.
<br>


## Simulator Composition

The simulator mainly consists of a 3-axes motion platform and a simulation software. Users put on a VR HMD and lie on their stom at a skeleton model on the platform. During the sliding, users are able to controll the virtual skeleton through their postures on several pressure sensors. If you are interested in the implementation of this controller, please read [this]().

The simulation software will capture the position and status of the virtual skeleton, and send them to the motion platform. Meanwhile, the motion platform will recreate the sliding status.

![p1](https://s1.imagehub.cc/images/2024/12/16/c675ba573aff579eca47b01d6efc0692.md.png)

## Simulation Software

This is the main part of my work in this project. A game engine, Unreal Engine 4, was employed for the development of the simulation software. 

We modified a Vehicle model to simplify the physical simulation of skeleton sliding. In our model, users' input will be regarded as lateral forces to control the steering. During the sliding, virtual skeleton's velocity and postures will be captured and send to the motion platform via User Datagram Protocol. 

![p2](https://s1.imagehub.cc/images/2024/12/16/ff8093b95ad1ee1cce31a4627a8cb1e6.md.png)

<br>

![p3](https://s1.imagehub.cc/images/2024/12/16/8561a5a54ae6359adc730728c38da398.md.png)