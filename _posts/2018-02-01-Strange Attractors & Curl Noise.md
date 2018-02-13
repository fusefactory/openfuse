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







In this video the particles are affected only by the curl noise function. 


{% include video id="251110884" provider="vimeo" %}


### Adding the attractor ###
Then I decided to add at this setup the behavior of the attractors I studied earlier. Obviously the main interest was to 

![]({{site.baseurl}}/images_posts/curl_attractor_001.png)

![]({{site.baseurl}}/images_posts/curl_attractor_002.jpg)

![]({{site.baseurl}}/images_posts/curl_attractor_003.jpg)

{% include video id="251112065" provider="vimeo" %}
