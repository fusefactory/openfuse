---
published: true
title: 'OF: good quality screen capture'
author: Luca Camellini
tags:
  - openframeworks
  - tools
categories:
  - coding
img: '({{ site.url }}/images_posts/of_capture.png)'
---
{% include video id="216023820" provider="vimeo" %}

Looking among many (almost all) alternatives in video capture systems to record openFrameworks video output, I finally found what is for me a good solution.

Thanks to [Timothy Scaffidi](https://github.com/timscaffidi) to implement ofxVideRecorder OF addons.

ofxVideoRecorder needs [ffmpeg](http://ffmpeg.org), the cross-platform tool for AV conversion. To install it you can use [homebrew](https://brew.sh) from OSX Terminal:

```
brew install ffmpeg
```

If you haven't **homebrew** yet on your system just type this on Terminal:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Now you can download [ofxVideoRecorder](https://github.com/timscaffidi/ofxVideoRecorder) and put it inside your OF addons folder. With OF projectGenerator add it to your project.
To record you screen capture as a movie you need to add this lines to some of your main project files.

**ofApp.h (include)**
```
#include "ofxVideoRecorder.h"
```

**ofApp.h**
```
    ofxVideoRecorder    vidRecorder;
    bool 				bRecording = false;
    ofFbo 				recordFbo;
    ofFbo 				recordFboFlip;
    ofPixels 			recordPixels;
```


**ofApp.cpp** - inside setup()  (check if the parameters of your capture are "prores" (codec), "2000K" (bitrate), ofGetWidth() (width), ofGetHeight() (height))
```
    vidRecorder.setVideoCodec("prores");
    vidRecorder.setVideoBitrate("2000k");
    recordFbo.allocate(ofGetWidth(), ofGetHeight(), GL_RGB);
    recordFboFlip.allocate(ofGetWidth(), ofGetHeight(), GL_RGB);
```


**ofApp.cpp** - inside update()
```
    recordFboFlip.getTexture().readToPixels(recordPixels);
    if(bRecording){
        bool success = vidRecorder.addFrame(recordPixels);
        if (!success) {
            ofLogWarning("This frame was not added!");
        }
    }
```


**ofApp.cpp** - inside draw()
```
    recordFbo.begin();
    ofClear(0,0,0,255);

	//put your draw code here
    
    recordFbo.end();
    
    //the following 3 rows need to vertical flip of the fbo.. wout you record (and show on screen) your output upside down
    recordFboFlip.begin();
    recordFbo.draw(0,recordFbo.getHeight(),recordFbo.getWidth(),-recordFbo.getHeight());
    recordFboFlip.end();
    
    //need if you want to visualize your output on the screen
    recordFboFlip.draw(0,0,recordFbo.getWidth(),recordFbo.getHeight());
```


**ofApp.cpp** - inside keyPressed(int key)
```
	if(key=='R'){
        bRecording = !bRecording;
        if(bRecording && !vidRecorder.isInitialized()) {
        // insert your path in the following row and your settings 1920x1080 60fps - the last 2 parms are zero because in my case I don't want to record audio..
 vidRecorder.setup("$HOME/Desktop/grab_"+ofGetTimestampString()+".mov", recordFbo.getWidth(),recordFbo.getHeight(), 60, 0, 0);
                       
            // Start recording
            vidRecorder.start();
        }
        else if(!bRecording && vidRecorder.isInitialized()) {
            vidRecorder.setPaused(true);
        }
        else if(bRecording && vidRecorder.isInitialized()) {
            vidRecorder.setPaused(false);
        }
    }
    if(key=='C'){
        bRecording = false;
        vidRecorder.close();
    }
```

Please change the file path to avoid errors.

That's all. You can now run your project and start recording pressing 'R' on your keyboard. With 'C' you stop and save the recording.
