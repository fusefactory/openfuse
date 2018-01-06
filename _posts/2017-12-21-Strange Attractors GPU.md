---
published: true
---
## Strange Attractors GPU

![]({{site.baseurl}}/images_posts/Burke_Shaw_Attractor.jpg)
<div style="text-align: right"> Burke-Shaw Attractor </div>



Attractors have been out for a while and as it happens in these cases there're a lot of resources on the internet involving this subject. They are marvellous mathematical objects pretty easy to program that give rise to interesting elegant outcomes. For a deeper understading of what an attractor is I suggest a quick look to the [wiki page](https://en.wikipedia.org/wiki/Attractor) and the [Softology's blog page](http://softology.com.au/tutorials/attractors2d/tutorial.htm). 

The visualization of a strange attractor displays the phase space of a chaotic dynamical system. They are defined by a set of differential equations solved by approximating the system of equations using something like the Euler's method or the Runge-Kutta method. To put it simply I used a particle system with starting random position for every element in the simulation and calculate the next position with a set of simple iterated functions that gives the next particle's position.


