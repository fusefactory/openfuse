---
published: true
---
## Pure Data in Mobile apps (iOS - Android)

**Pure Data** (or just Pd) is an open source visual programming language for multimedia.

A simple way to implement generative audio inside mobile app is using [Pure Data](https://puredata.info/). Pure data for mobile is available for [iOS](https://github.com/libpd/pd-for-ios) and for [Android](https://github.com/libpd/pd-for-android). The interesting thing is that you can use the same Pure Data files (.pd) for all platform: **Windows**, **Mac OS**, **iOS** and **Android**.


The communication from mobile **mobile apps** and **pure data**  uses libpd messages integrated in the framework.

**Pros**
- use same .pd patch for all OS
- simple communication using libpd messages
- opensource

**Cons**
- it is compatible only with .wav files (it can’t play .mp3, .acc etc)
- slow to create complex pd patch


Now we create a simple PD patch and a Xcode project to show how integrate [pd-for-ios](https://github.com/libpd/pd-for-ios).

### Pure Data patch .pd
This is a sample Pure Data patch. It generate a 440Hz sin wave when "s onOff" is active.

```c
#N canvas 452 527 450 300 10;
#X obj 170 76 *~ 0.25;
#X obj 170 98 *~;
#X obj 171 129 dac~;
#X obj 170 44 osc~ 440;
#X obj 240 34 s onOff;
#X obj 240 10 tgl 15 0 empty empty empty 17 7 0 10 -262144 -1 -1 0
1;
#X obj 228 81 r onOff;
#X connect 0 0 1 0;
#X connect 1 0 2 0;
#X connect 1 0 2 1;
#X connect 3 0 0 0;
#X connect 5 0 4 0;
#X connect 6 0 1 1;
```

![pure_data2.png]({{site.baseurl}}/images_posts/pure_data2.png)

### Retrive pd-for-ios
First clone [GitHub repo](https://github.com/libpd/pd-for-ios).  From terminal:

```Bash
git clone https://github.com/libpd/pd-for-ios
cd pd-for-ios/
git submodule init
git submodule update --init --recursive
```
These two commands install the dependencies from libpd.
```Bash
git pull
git submodule update --recursive
```

### Xcode project

Add subproject libpd.xcodeproj, located in pd-for-ios folder, to current project (drag and drop).
Build libpd-ios choosing “libpd-ios” in target and build.

In main project go to TARGETS > Linked Frameworks and Libraries > add:
- libpd-ios.a
- AudioToolbox.framework
- AVFoundation.framework

In TARGETS, Build Settings, Header Search Path add:
```
../pd-for-ios/libpd/objc/
```

![XcodeProjectGif]({{site.baseurl}}/images_posts/pure-data.gif)


!!! **If you use Xcode 9 check [this](https://github.com/libpd/pd-for-ios/issues/19) current bug.** !!!

Create a new group "PureData" and copy "test-iOS.pd"

**AppDelegate.h**

Import PdAudioController library and create a PdAudioController property.

```Objective-C

#import "PdAudioController.h"
@property (strong, nonatomic) PdAudioController *pdAudioController;
```

**AppDelegate.m**

Allocate PdAudioController property, configure pd ambient and set active PdAudioController when application become active.

```Objective-C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    self.pdAudioController = [[PdAudioController alloc] init];
    PdAudioStatus pdInit = [self.pdAudioController configureAmbientWithSampleRate:44100 numberChannels:2 mixingEnabled:YES];
    
    if (pdInit != PdAudioOK){
        NSLog(@"Pd failed to initialize");
    }
    return YES;
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
    self.pdAudioController.active = YES;
}
```


The goal is to active our pd sound using a iOS switch button.
Create a UISwitch in storyboard (or in your xib) and connect "Value Changed" to method "onSwitchChange" in you viewController (ex. ViewController.m).

We create a simple PDPatch class. It implements the file loader and the communication with pd.

**PDPatch.h**
```Objective-C
#import <Foundation/Foundation.h>
#import "PdDispatcher.h"

@interface PDPatch : NSObject

- (instancetype) initWithFile:(NSString *) pdFile;
- (void) onOff:(BOOL) yesNo;

@end
```

**PDPatch.m**
```Objective-C
#import "PDPatch.h"

@implementation PDPatch

- (instancetype) initWithFile:(NSString *)pdFile{
    self = [super init];
    if (self){
        void * patch = [PdBase openFile:pdFile path:[NSBundle mainBundle].resourcePath];
        if (! patch){
            NSLog(@"Failed to load patch %@", pdFile);
        }
    }
    
    return self;
}

- (void) onOff:(BOOL) yesNo{
    float yn = (float)yesNo;
    [PdBase sendFloat:yn toReceiver:@"onOff"];
}

@end

```

**MainViewController.h** 

Include PDPatch.h and create a PDPatch property.
```Objective-C
#import "PDPatch.h"
@property PDPatch *pdPatch;
```

**MainViewController.m** 

Load PDPatch.
```Objective-C
self.pdPatch = [[PDPatch alloc] initWithFile:@"test-iOS.pd"];
```
Create the IBAction method:
```Objective-C
- (IBAction)onSwitchChange:(id)sender {
        [self.pdPatch onOff: [sender isOn]];
}
```


Xcode project is downloadable [here](https://github.com/fusefactory/openfuse/blob/master/zip_posts/Example-PureData.zip).

Thanks to  [cheetomoskeeto](https://www.youtube.com/channel/UC-RatzHn1ukuuINLqnbBYeg) youtube channel for his tutorial.
