---
published: false
---
## Pure Data in Mobile apps (iOS - Android)

**Pure Data** (or just Pd) is an open source visual programming language for multimedia.

The easiest way to implement generative audio inside mobile app is using [Pure Data](https://puredata.info/). Pure data for mobile is available for [iOS](https://github.com/libpd/pd-for-ios) and for [Android](https://github.com/libpd/pd-for-android). The interesting thing is that you can use the same Pure Data files (.pd) for all platform: **Windows**, **Mac OS**, **iOS** and **Android**.


The communication from mobile **mobile apps** and **pure data**  use libpd messages integrated in the framework.

**Pros**:
- use same .pd patch for all OS
- simple communication using libpd messages
- opensource

**Cons**:
- it is compatible only with .wav files (it can’t play .mp3, .acc etc)


Now we create a simple PD patch and a Xcode project to show how integrate [pd-for-ios](https://github.com/libpd/pd-for-ios).

### a) Pure Data patch .pd
This is a sample Pure Data patch. It generate a 440Hz sin wave when "s onOff" is active

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

![pure_data1.png]({{site.baseurl}}/images_posts/pure_data1.png)


