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

The paper [How a life-like system emerges from a simple particle motion law](https://www.nature.com/articles/srep37969) by Thomas Schmickl, Martin Stefanec & Karl Crailsheim 



Pseudo code:
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


{% include video id="250002549" provider="vimeo" %}

{% include video id="250002549" provider="vimeo" %}
