---
published: true
title: 'VR Development: Cinder-VR VS Aframe'
author: Mark
tags:
  - vr
  - cinder
  - webvr
  - aframe
categories:
  - coding
img: images_posts/2017-07-12-florim-vr.gif
---

## Florim Cinder Demo
* bitbucket: [https://bitbucket.org/fuseinteractive/florimvrdemo](https://bitbucket.org/fuseinteractive/florimvrdemo)
* git: [git@bitbucket.org:fuseinteractive/florimvrdemo.git](git@bitbucket.org:fuseinteractive/florimvrdemo.git)

### Uses
* [Cinder](https://libcinder.org/) - _framework, similar to OpenFrameworks_
* [Cinder-VR](https://github.com/cinder/Cinder-VR) - _Cinder block: "Virtual reality support for Oculus Rift and HTC Vive in Cinder!"_
* ( [Cinder-poScene](https://github.com/Potion/Cinder-poScene) - _scene graph_ )

![FlorimDemoGif]({{site.baseurl}}/images_posts/2017-07-12-florim-vr.gif)


### VR video
To get the video part to work I only really needed to get Cinder to build with the Cinder-VR block (and on windows; oculus is not supported on Mac). The combination of not having too much experience with Cinder and also not normally developing in Visual Studio made this a bit of a pain-in-the-ass, but all in all in wasn't very difficult. One thing that I _think_ is pretty common for a Cinder developer, but less so if you are used to work with OpenFrameworks, is that I had to first open the Cinder-VR block's project file and _build_ the block so there are binary lib files on the development machine, which are then used when building the Cinder applications that use the block. This probably speeds up the build process for the applications, but is an extra step you kind of just need to know about. You also have to perform this step for every platform (32-bit/64-bit) and target (debug/release) separately.

Another issue is that the actual VR-stuff isn't always available (on mac Oculus Rift isn't even supported, and often you want to be able to develop and test without having to connect VR glasses). For this, I created a VR_ENABLED compiler flag, which is enabled for windows machines (at compile time) and disabled for Mac machines (at compile time). When the flag is disabled, a standard Virtual Camera object is used as a "replacement" for the VR glasses.

This is what that looks like in the setup method of the main application class:

```c++
#ifdef VR_ENABLED
    {   CI_LOG_I("Setting up VR...");

        try {
            ci::vr::initialize();
        }
        catch (const std::exception& e) {
            CI_LOG_E("VR failed: " << e.what());
        }

        try {
            mVrContext = ci::vr::beginSession(
                ci::vr::SessionOptions().setOriginOffset(vec3(0, 0, -3))
                    .setControllerConnected([this](const ci::vr::Controller* controller) {
                        CI_LOG_I("VR controller connected");
                        this->pController = controller;
                    })
                    .setControllerDisconnected([this](const ci::vr::Controller* controller) {
                        CI_LOG_I("VR controller DISconnected");
                        this->pController = NULL;
                    })
            );
        }
        catch (const std::exception& e) {
            CI_LOG_E("VR Session failed: " << e.what());
        }

        if (mVrContext) {
            try {
                mHmd = mVrContext->getHmd();
                //mHmd->setMirrorMode(ci::vr::Hmd::MirrorMode::MIRROR_MODE_STEREO);
                mHmd->setMirrorMode(ci::vr::Hmd::MirrorMode::MIRROR_MODE_UNDISTORTED_STEREO);
            }
            catch (const std::exception& e) {
                CI_LOG_E("VR Hmd failed: " << e.what());
            }
        }
    }
#else
    {   CI_LOG_I("Setting up VR-replacement camera...");
        mCamUi = CameraUi( &mCam );
    }
#endif
```

And here's the main draw routine:
(obviously all the actual drawing happens in drawScene, where you can do exactly the same for normal screen output as for VR-output):
```c++
void CinderApp::draw() {
    gl::clear(Color(0.02f, 0.02f, 0.1f));

    #ifdef VR_ENABLED
        // render for VR glasses?
        if(mHmd){
            // render for individual eyes
            mHmd->bind();
            {
                for (auto eye : mHmd->getEyes()) {
                    mHmd->enableEye(eye);
                    drawScene();
                    mHmd->drawControllers(eye);
                }
            }
            mHmd->unbind();

            // ??
            if (drawMirrored) {
                mHmd->submitFrame();
                gl::viewport(getWindowSize());
                mHmd->enableEye(ci::vr::EYE_HMD);
                drawScene();
            } else {
                gl::viewport(getWindowSize());
                gl::setMatricesWindow(getWindowSize());
                mHmd->drawMirrored(getWindowBounds(), true);
            }

            if(bDrawDebug)
                drawDebug();
            return;
        }
    #endif

    // draw "normal" (no VR)
    gl::viewport(getWindowSize());
    gl::setMatrices(mCam);
    drawScene();

    if(bDrawDebug)
        drawDebug();
}
```

Note that when VR_ENABLED == true, it will _try_ to render to the VR HMD (Head Mounted Display), but if there is no HMD available, it will fall back to rendering "normally", the same way is when VR_ENABLED == false.



### Spatial Audio
Cinder communicates through OSC with an independent MaxMSP patch that uses the [Hoa Library](http://www.mshparisnord.fr/hoalibrary/en/) for implementing Ambisonic (spatial) audio. There are also [OpenFrameworks](https://github.com/CICM/ofxHoa) and [Cinder](https://github.com/saynono/Cinder-Hoa) bindings for the Hoa library, which would make it possible to integrate the audio system into the main application, but I haven't been able to get them to work so far...

##### How the communication between the cinder app and max currently works is:
* Cinder sends a trigger message for a certain audio sample
* Cinder sends position information (pitch/yaw/distance) for the currently active audio object

This isn't ideal because it's pretty difficult to support multiple simultaneous audio samples this way, and it would be unclear how long the cinder app should keep sending position information for every audio object.

##### How the communication should probably work in a more robust setup:
* Cinder send a trigger message, including a 3D (x,y,z) position for and audio object
* Cinder constantly sends listener's orientation

Cinder simply always provides the audio module with the _listener's_ current orientation (not the sound-source's orientation). And sound objects are triggered and positioned ("spawned") at a 3D position. This would require the audio module (the max application) to do some additional calculations as it would have to calculate the pitch/yaw/distance attributes (which is the data used by the Hoa library) for each active sound object based on the listener's orientation and the relative position of the sound objects to the listener.






___



## Aframe test
* github: [https://github.com/fusefactory/Aframe-WebVR-test](https://github.com/fusefactory/Aframe-WebVR-test)
* git: [git@github.com:fusefactory/Aframe-WebVR-test.git](git@github.com:fusefactory/Aframe-WebVR-test.git)
* live demo: [https://fusefactory.github.io/Aframe-WebVR-test](https://fusefactory.github.io/Aframe-WebVR-test/)

### Uses
* [Aframe](https://aframe.io/) - _Mozilla's WebVR framework_ ([github](https://github.com/aframevr/aframe))
* [a-frame-look-at-component](https://github.com/ngokevin/aframe-look-at-component) - _aframe addon for making objects always face the camera
* ( [a-frame-leap-hand](https://github.com/openleap/aframe-leap-hands) - _aframe addon for using Leap Motion (not visible in gif below)_ )

![AframeTestGif]({{site.baseurl}}/images_posts/2017-07-12-aframe.gif)


### Fast prototyping
Aframe lets you build you 3D scene like you build a web-page; using the HTML and JavaScript. It's a very declarative way of programming which is _very_ different from writing a C++ application (potentially MUCH faster, but also very opiniated). I was able to put some stuff together and get it working really fast. Adding leap motion for example was literally just a matter of adding the addon js file and two lines of HTML:

```html
<script src="lib/aframe-leap-hands.min.js"></script>

<a-entity leap-hand="hand: left;"></a-entity>
<a-entity leap-hand="hand: right;"></a-entity>
```

This already gave me the 3D robot arms. (I will need to do a bit more to actually get some interaction with objects).

Aframe is _great_ for quickly throwing a little scene together to try something and seems a good tool for fast-prototyping. You don't have to think about low-level technical stuff like texture-loading (you simply add an <img> tag) and it has a pretty sophisticated animation system for building for example transitions. I still have to see how Aframe holds up when you want to start building more complicated applications...

### Multi Platform
In my experience so far, Aframe works -and performs- pretty good on both desktop and mobile browsers, so you can easily develop and try it out in google cardboard/daydream. WebVR is a standard that's still under development, but there are already some browsers with (beta) support for Oculus Rift and HTC Vive (see [https://webvr.info/](https://webvr.info/) for up-to-date information on the status of the different platforms). This means that with the right browser (and settings) you should be able to also run an aframe application inside a VR headset, but I haven't tried it yet.

Another awesome "feature" of WebVR is that you can simply upload your app to a website or -for example- github page and everybody can try it on their computer/phone/VR-headset, which is great for sharing updates easily between team-members and with clients.

### Spatial Audio in WebVR
I haven't tried web-based spatial audio it yet, [but it's definitely possible](http://smus.com/spatial-audio-web-vr/).

___


Post written using the [Atom text editor](https://atom.io/) and the [Markdown Preview package](https://atom.io/packages/markdown-preview).

Screen captures created the Quicktime Player and turned into gifs using ffmpeg and gifsicle
```
ffmpeg -i florim-vr.mov -t 9 -s 718x435 -pix_fmt rgb24 -r 10 -f gif - | gifsicle --optimize=3 --delay=1 > florim-vr.gif
```
