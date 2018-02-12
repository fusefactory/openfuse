---
published: true
author: Samuel Pietri
tags:
  - Openframeworks
  - GLSL
  - Attractors
  - Curl Noise
categories:
  - Particle system
img: 'https://fusefactory.github.io/openfuse/images_posts/curl+strA.jpg'
video: 'url https://vimeo.com/251449477'
title: Strange Attractors & Curl Noise
abstract: >-
  Implementation of curl noise forces and strange attractors into a particle
  system.
---

![]({{site.baseurl}}/images_posts/curl+strA.jpg)




Following the work done in [this previous post](https://fusefactory.github.io/openfuse/strange%20attractors/particle%20system/Strange-Attractors-GPU/) I tried to widen the aesthetic possibilities given by the strange attractors by adding other behaviors to the particle system. In particular I wanted to give the system a more "fluid" feeling without any complex physical simulation going on but taking advantage once again of the noise function. There's a specific function commonly known as "curl noise" that is perfectly suited for this purpose and that is the main reason I tried to implement that one into the system.

This technique is able to fake velocity fields for a procedural fluid flow called “Curl Noise” created by Robert Bridson, and this is how we found this paper. In this paper the author defines a divergence free noise suitable for velocities simulations.

Using divergence free potentials is important for a flow simulation because it avoids sinks in the flow (try to think that with this you will not get all the particles going to a single final position, like a hole in space). Implementing is quite simple because you only have to calculate the differentials of the potentials (noises) used to create your field.

To create the potentials used for the velocities we used the equations from the next image. The only assumption that we made is that every scalar component from the potential vector field is a 2D function that would satisfy the rotor operator.



The second group of equations explains our assumption, we defined each axis potential as a function of the other two axes in order to use 2D textures for the tree potentials, this way we do not need 3d volume textures for each potential. With this assumption we ended with tree 2d textures that defined our potential so the velocity would be defined as the rotor operator applied to the potentials field as you can see in the equations. To calculate the partial derivates we only had to calculate the finite difference the given textures, and the good thing is that since we have our point defined in a texture space we could use this position to get the potential value for the tree defined potentials.

Now that we know how to create the velocity field we needed tree good noise textures to work with, this is an issue because if we load some external textures our development would be tied to those images. So we decided to create the noise textures by ourselves.

Our first approach was to make textures using perlin noise functions, but we then found that they are somehow expensive to calculate so we opted for a more practical solution, simplex noise, and the best thing is that we found one webGL  hack here. With the noise functions solved we only needed to make it turbulent, so we found that we could add higher frequencies to get a turbulent noise, you can read a full explanation here.





In this video the particles are affected only by the curl noise function. 


{% include video id="251110884" provider="vimeo" %}


### Adding the attractor ###
Then I decided to add at this setup the behavior of the attractors I studied earlier. Obviously the main interest was to 

![]({{site.baseurl}}/images_posts/curl_attractor_001.png)

![]({{site.baseurl}}/images_posts/curl_attractor_002.jpg)

![]({{site.baseurl}}/images_posts/curl_attractor_003.jpg)

{% include video id="251112065" provider="vimeo" %}
