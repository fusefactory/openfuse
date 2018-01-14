---
published: true
author: Samuel Pietri
tags:
  - Openframeworks
  - GLSL
  - Emergence
categories:
  - Particle system
img: images_posts/pDm_cover_vimeo.jpg
title: Primal Digital Medium
---
{% include video id="251051717" provider="vimeo" %}


The paper [How a life-like system emerges from a simple particle motion law](https://www.nature.com/articles/srep37969) by Thomas Schmickl, Martin Stefanec & Karl Crailsheim describes a simple motion law for moving and interacting particles leading to a self-structuring, self-reproducing and self-sustaining life-like system. That's the way they describe it in the article and it definetily cought my attention. These emerging patterns look like really basic organism. Some structures emerge and grow over time while the particles interact with each other.
So I was interested in implementing the system in a simulation program and explore its aesthetic potential.
For all the technical detail make sure you read the article. I'll just outline the main framework of the setup leaving most of the detail for further readings.
So unlike other simple computational models, such as cellular automata, this system is able to produce natural-like structures and dynamics without any restriction to unnatural modeling constraints. 
The particles respect only this motion rule, described by this pseudo code here:

```
loop foreach timestep{
	loop foreach particle{
    L = (count other particles in left semicircle with radius r)
    R = (count other particles in right semicircle with radius r)
    N = L + R
    delta_phi = alpha + beta * N * sign(R - L)
    rotate delta_phi # positive values: rotate to the right
    move-forward v
    }
}

```

Each particle determines the number of its neighbors on the left side (all the particles inside a semicircle with radius r) and on the right side(all the particle inside the other semicircle of radius r). The change of its heading angle (delta_phi) every time step is the responsible of the particle's motion. After the rotation each particle moves forward with a fixed velocity.

The key formula to make the system working properly is the one that calculates the angle of rotation.


![]({{site.baseurl}}/images_posts/pDm_formula.png)

They discovered an interesting system with the parameter set:
```
r =5
alpha = 180°  	
beta = 17°
v = 0.67

with a density(particles/space unit) of 0.08
```
In this situation structures similar to cell start to form randomly from the particle soup.

Given these information I then implemented the algorithm in openFrameworks.

![]({{site.baseurl}}/images_posts/pDm_001.png)

