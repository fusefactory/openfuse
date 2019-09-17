---
published: false
---
{% include video id="216023820" provider="vimeo" %}

We find another solution to record openFrameworks video ouput that solve the restriction to have specific resolution 1920x1080 or less.

Thanks to [Nick Hardeman](https://github.com/NickHardeman) to implement ofxFFmpegRecorder OF addons.

ofxFFmpegRecorder needs [ffmpeg](http://ffmpeg.org), the cross-platform tool for AV conversion. To install it you can use [homebrew](https://brew.sh) from OSX Terminal:

```
	brew install ffmpeg
```

If you haven't **homebrew** yet on your system just type this on Terminal:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Now you can download [ofxFFmpegRecorder](https://github.com/NickHardeman/ofxFFmpegRecorder) and put it inside your OF addons folder. 

In order to speed up the fbo reading process we use [ofxFastFboReader](https://github.com/satoruhiga/ofxFastFboReader) of [Satoru Higa](https://github.com/satoruhiga). Download and put it inside your OF addons folder.

With OF projectGenerator add these two addons to your project.

To record you screen capture as a movie you need to add this lines to some of your main project files.

**ofApp.h (include)**
```
#include "ofxFFmpegRecorder.h"
#include "ofxFastFboReader.h"
```

**ofApp.h**
```
    ofxFFmpegRecorder    vidRecorder;
    ofFbo                recordFbo;
    ofFbo 				 recordFboFlip;
    ofPixels             recordPixels;
    ofxFastFboReader     reader;
```


**ofApp.cpp** - inside setup()  (check if the parameters of your capture are "prores" (codec), "2000K" (bitrate), ofGetWidth() (width), ofGetHeight() (height))
```
    vidRecorder.setup(true, false, glm::vec2(ofGetWidth(), ofGetHeight()));
    vidRecorder.setOverWrite(true);
    vidRecorder.setFFmpegPathToAddonsPath();
    vidRecorder.setFps(60);
    vidRecorder.setVideoCodec("prores");
    vidRecorder.setBitRate(2000000);
    
    recordFbo.allocate( ofGetWidth(), ofGetHeight(), GL_RGB );
    recordFboFlip.allocate( ofGetWidth(), ofGetHeight(), GL_RGB );
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
    
    if (vidRecorder.isRecording()) {
        //  ofxFastFboReader is used to speed this up :)
        reader.readToPixels(recordFboFlip, recordPixels);
        
        if (recordPixels.getWidth() > 0 && recordPixels.getHeight() > 0) {
            vidRecorder.addFrame(recordPixels);
        }
    }
    
    //need if you want to visualize your output on the screen
    recordFboFlip.draw(0,0,recordFbo.getWidth(),recordFbo.getHeight());
```


**ofApp.cpp** - inside keyPressed(int key)
```
   if (key == 'R') {
        if (vidRecorder.isRecording()) {
            // stop
            cout << "STOP RECORDING.." <<endl;
            vidRecorder.stop();
        }
        else {
            cout << "START RECORDING.." <<endl;
            ofFilePath f;
            string myPath = f.getUserHomeDir() + "/Desktop/grab_"+ofGetTimestampString()+".mov";
            cout << myPath << endl;
            vidRecorder.setOutputPath(myPath);
            vidRecorder.startCustomRecord();
        }
    }
```

That's all. You can now run your project and start and stop recording pressing 'R' on your keyboard.