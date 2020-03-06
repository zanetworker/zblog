---
title:  "Streaming a Video File on Android"
date:   2012-10-05 15:04:23
categories: [technical]
tags: [Android, Java]
---


Well,

I have been through multiple forums looking for the best way that works for me to  play a streamed video file from a server or from my local machine, and I found an easy way to do that, here is how:

```java

public void onCreate(Bundle icicle) {
    super.onCreate(icicle);
    getWindow().setFormat(PixelFormat.TRANSLUCENT);</code>

    yourVideoView = new VideoView(this);
    yourVideoView.setMediaController(new MediaController(this));
    setContentView(yourVideoView);

    // This works fine for almost all MPEG-4 and H.264 containers you find out there

    // If you have the video file on your sdk card

    yourVideoView.setVideoURI(Uri.parse("file:///sdcard/video.mp4"));

    // If you are getting the stream from an http Server let's say

    yourVideoView.setVideoURI(Uri.parse("The URL of the server, i.e: http:// ...."));
    yourVideoView.requestFocus();
    yourVideoView.start();
}
```

**N.B:** this might not work so well for your emulator, however this works well on a real device. If you insist on making this work on the emulator, then you might want to try this (Only for playing from a local source).
<ul>
	<li>Go to your platform-tools directory in your android-sdk</li>
	<li>copy the video file you want to this directory</li>
	<li>run "adb push <your_file> /sdcard/<target destination></li>
</ul>

I hope this helps
