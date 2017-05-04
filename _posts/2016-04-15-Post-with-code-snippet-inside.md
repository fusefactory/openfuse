---
published: true
author: Luca Camellini
tags:
  - test
  - dokk
  - interactive
---
{% include video id="216027647" provider="vimeo" %}

Code inside the post...

```
.highlight {
  margin: 0;
  padding: 1em;
  font-family: $monospace;
  font-size: $type-size-7;
  line-height: 1.8;
}
```

![Image of Ljos]({{site.baseurl}}/images/ljos.png)

Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.

![Image of Ljos]({{site.baseurl}}/images/stigmergy_dokk.png)

Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.

Below the vertex shader:

```
#version 330

uniform mat4 modelview;
uniform mat4 projection;

layout (location = 0) in vec4 position;
layout (location = 1) in vec4 velocity;
layout (location = 2) in vec4 density;

out vec4 vColor;

void main() {

    vec4 eyeVector = modelview * vec4(position.xyz,1.0);
    gl_Position = projection * eyeVector;

    gl_PointSize = density.x;

    vColor = vec4(0.95,0.0,0.0,0.05);
}
```
