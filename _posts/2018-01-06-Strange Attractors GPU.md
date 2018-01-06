---
published: true
author: Samuel Pietri
tags:
  - Openframeworks
  - GLSL
  - Differential Equations
categories:
  - Strange Attractors
  - Particle system
img: images_posts/Burke_Shaw_Attractor.jpg
---
![]({{site.baseurl}}/images_posts/Burke_Shaw_Attractor.jpg)
<div style="text-align: right"> Burke-Shaw Attractor </div>





Attractors have been out for a while and as it happens in these cases there're a lot of resources on the internet involving this subject. They are marvellous mathematical objects pretty easy to program that give rise to interesting elegant outcomes. For a deeper understading of what an attractor is I suggest a quick look to the [wiki page](https://en.wikipedia.org/wiki/Attractor) and the [Softology's blog page](http://softology.com.au/tutorials/attractors2d/tutorial.htm). 

The visualization of a strange attractor displays the phase space of a chaotic dynamical system. They are defined by a set of differential equations solved by approximating the system of equations using something like the Euler's method or the Runge-Kutta method. To put it simply I used a particle system with starting random position for every element in the simulation and calculate the next position with a set of simple iterated functions that gives the next particle's position.


The easiest way I found to implement the strange attractors was to do all the calculation inside a fragment shader that take care of defining the particle's position.

Here's an example of a fragment shader that calculates the particle's motion while affected by Lorenz attractor.

```
#version 330

// ping pong inputs
uniform sampler2DRect particles0;
uniform sampler2DRect particles1;

uniform float timestep;

in vec2 texCoordVarying;

layout(location = 0) out vec4 posOut;
layout(location = 1) out vec4 velOut;


// Lorenz Attractor parameters
float a = 10.0;
float b = 28.0;
float c = 2.6666666667;


void main()
{
    
    int id = int(texCoordVarying.s) + int(texCoordVarying.t)*int(textureSize(particles0).x);
    vec3 pos = texture(particles0, texCoordVarying.st).xyz;
    vec3 vel = texture(particles1, texCoordVarying.st).xyz;

    // Previous positions
    float x = pos.x;
    float y = pos.y;
    float z = pos.z;
    
   	// Increments calculation
    float dx = (a * (y - x))   * timestep;
    float dy = (x * (b-z) - y) * timestep;
    float dz = (x*y - c*z)     * timestep;
    
	// Add the new increments to the previous position
    vec3 attractorForce = vec3(dx, dy, dz) ;
    pos +=attractorForce;

    
    
    posOut = vec4(pos, id);
    velOut = vec4(vel, 0.0);
}
```

Down here a few more examples of attractors and their relative set of differential equations.



##Aizawa Attractor##

![]({{site.baseurl}}/images_posts/Aizawa_Attractor.png)


##Arneodo Attractor##

![]({{site.baseurl}}/images_posts/Arneodo_Attractor.png)


##Dadras Attractor##

![]({{site.baseurl}}/images_posts/Dadras_Attractor.png)


##Dequan Attractor##

![]({{site.baseurl}}/images_posts/Dequan_Li_Attractor.png)


##Lorenz Attractor##

![]({{site.baseurl}}/images_posts/Lorenz_Attractor.png)


##Lorenz Mod 2 Attractor##

![]({{site.baseurl}}/images_posts/Lorenz_mod2_Attractor.png)


##Thomas Attractor##

![]({{site.baseurl}}/images_posts/thomas_attractor.png)





