---
published: true
author: Samuel Pietri
tags:
  - Openframeworks
  - GLSL
  - Emergence
categories:
  - Particle system
img: images_posts/pDm_001.png
title: Primal Digital Medium
---
![]({{site.baseurl}}/images_posts/pDm_001.png)

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

<img src="images_posts/pDm_formula.png" alt="Drawing" style="width: 200px;"/>



{% include video id="250002549" provider="vimeo" %}

{% include video id="250002549" provider="vimeo" %}
