---
published: false
author: Mark
tags:
  - Openframeworks
  - Orbbec Persee
  - Kinect
  - Depth Sensors
categories:
  - R&D
img: images_posts/orbbec_kinect.png
title: ofxDepthStream
---
![]({{site.baseurl}}/images_posts/orbbec_kinect.png)

This was initially just a little R&D project to research the possibilities of using the Orbbec Persee as a replacement for the Kinect as the latter has [officially been discontinued](https://www.fastcodesign.com/90147868/exclusive-microsoft-has-stopped-manufacturing-the-kinect).

Anticipating future use and the possibility to use different depth-sensors in the same project, this has turned into a [device-agnostic tool-set](https://github.com/fusefactory/ofxDepthStream) for using depth sensor image-streams streamed over a network.

### Installing Ubuntu
The first step was getting the Orbbec Persee “to work”. Initial attempts to create applications for the Android operating system that comes pre-installed on the Persee proved very complicated. A lot of people on Orbbec’s official forum also seemed to suggest that it’s better to install Ubuntu on the Persee, so that’s what I ended up doing.


### Building a sample application on the orbbec
Having Orbbec's Ubuntu 16.04 image up and running on the persee, first I tried to build any of the example applications in the latest version of the Orbbec Astra SDK for Linux. Unfortunately I didn’t succeed and the documentation didn't provide any path forward, so next I tried Orbbec’s OpenNI SDK, with which I was finally able to build a functional application that gets a depth-image stream from the Persee’s sensor.


### Network Transmitter/Receiver
Being able to read realtime camera data, the next step was to stream this data over a network. fuse* has been using multiple Kinect network streams in their live-performance shows for a couple of years, so I could use the existing tested & proven protocol, which looks like this;
* Transmitter application listens for incoming connection from a client on a TCP port
* Client connects (using the transmitter’s IP-address/hostname and network port)
* Transmitter starts sending  depth-image frames
* Frames are compressed using zlib
* Every frame consists of
  * a 4-byte header which specifies the size (in bytes) of the main data block
  * the main data block containing the frame's compressed image-data
* Client disconnects
* Transmitter stops sending depth-image frames and starts listening for new connections

![orbbec1.png]({{site.baseurl}}/images_posts/orbbec1.png)

_Receiving the first data over a network from an Orbbec Persee; the depth-stream data wasn’t being properly processed into a grayscale image yet._

### DepthStream library
All of the code that was written for sending and receiving the compressed depth-image stream over a network is bundled in the DepthStream c++ library, which provides a single [depth::](https://fusefactory.github.io/ofxDepthStream/docs/html/namespacedepth.html)  namespace which contains all the classes and functions necessary for performing the tasks: compressing, transmitting, receiving, inflating and later also recording and playback. This library does not contain OpenFrameworks related code and the only dependencies are the c++ standard library (c++11; it uses lambdas a lot) and the zlib library for compression and inflation.

### ofxDepthStream addon
For now OpenFrameworks is the primary software development framework used in fuse* projects. Therefore an OpenFrameworks addon was the first choice to wrap everything we need to use the depth-sensors in a project into a re-usable package. Having 90% of the functionality already covered in the before-mentioned DepthStream library, the only OpenFrameworks-specific code (in the [ofxDepthStream::](https://fusefactory.github.io/ofxDepthStream/docs/html/namespaceofx_depth_stream.html) namespace) in the addon is the code that uses the depth-stream data to populate ofTexture and ofMesh instances.

## Example applications
The example applications in the addon weren’t only written for documentation and demonstration purposes, but we basically written while their respective features were being developed.

### A simple client that loads the depth-stream into a texture
This was the obvious first client application that was written. Feature-wise it’s very simple; it receives a depth-image stream, turns it into a texture and draws it to the screen. Aside from making sure these features work however, some effort was also made to make the API both as simple as possible and as flexible as possible, while also keeping performance in mind.

The result is that it only requires only one custom variable and two lines of code to have a realtime-depth image stream that can be drawn to the screen;

```
// our network stream receiver
depth::ReceiverRef depthReceiverRef;
// start our receiver
depthReceiverRef = depth::Receiver::createAndStart(“persee.local”, 4445);
// load incoming data into a texture
ofxDepthStream::loadDepthTexture(*depthReceiverRef, depthTex);
````

### A simple client that loads the depth-stream into a mesh
While the simple client example app shows the very minimal implementation, this application demonstrates the fleixiblity of the API; showing support for a workflow with two different uses of the same depth-image stream; loading both a texture and a mesh.

Using the emptyAndInflateBuffer method a we check for new data and if we’ve received a new frame it is decompressed. The resulting data is used to populate both a texture (using the loadDepthTexture method) and a mesh (using the loadMesh method).

![orbbec2.png]({{site.baseurl}}/images_posts/orbbec2.png)
_Mesh example app; using depth-stream data to populate both a texture and a mesh_

```
// process (decompress and load) every incoming frame once
depth::emptyAndInflateBuffer(*receiverRef, [this](const void* data, size_t size){
  // load the received data into a grayscale texture
  if (bDrawDepth) {
    ofxDepthStream::loadDepthTexture(depthTex, data, size);
  }

  // load the received data into our point-cloud mesh
  if (bDrawPoints) {
    ofxDepthStream::loadMesh(mesh, data, size, ofxDepthStream::MeshLoaderOpts()
      .setDepthFactor(depthFactor));
  }
});
```

### A record/playback demo
To optimize workflows and enable development even when the depth-sensors are not available it is useful to be able to record and playback depth image streams, so basic recording and playback functionality is featured in the DepthStream library.
Because this step required different information streams coming from either the network or a playback instance, going either directly to the screen or first through a recorder instance, it helped design some intermediate utility classes (the depth::Frame and depth::Buffer classes) which now make it easy to (re-)connect the various parts of the application in way that feels like the coder’s version of connecting audio-cables.


### A dual-stream benchmark app
This application was a first test of using two different network streams simultaneously. Because we mostly stream over wifi during development, performance quickly became an issue and quantitative benchmark tools were added to help us optimize the use of our bandwidth.



![orbbec_kinect.png]({{site.baseurl}}/images_posts/orbbec_kinect.png)
_The DualStream Benchmark app with an Orbbec Persee (left) streaming 16-bit depth-stream at 640x480 and a Kinect V2 (right) streaming 32-bit at 512x424._


### An advanced touch-detector application (WIP)

Currently we’re researching if/how we can use depth-sensors to turn an ordinary wall into a touch-screen. The code for this research is written as yet another example application in the addon, but in a private repository (for now). Those results will be saved for a later post...

![orbbec3.png]({{site.baseurl}}/images_posts/orbbec3.png)

_WIP; using depth-sensors to turn a regular wall into a touch-screen_
