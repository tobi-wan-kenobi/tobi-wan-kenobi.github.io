---
layout: post
title:  "Using an Android phone as IP Webcam on Void Linux"
date:   2020-12-28 13:25:24 +0100
author: tobi-wan-kenobi
categories: linux
tags: [ linux, void-linux ]
---

## Motivation

I wanted to use my mobile phone as web cam, because it has a half-way decent
camera, and in Covid times, decent web cams get expensive.

## Solution

Inspiration: [github:bluezio/ipwebcam-gst](https://github.com/bluezio/ipwebcam-gst/)

Assumes that you have WiFi on your mobile phone

1. Install [Google Play:IP Webcam](https://play.google.com/store/apps/details?id=com.pas.webcam) on your Android phone
2. Configure it (pretty straight-forward) and start the server
3. On your Void Linux machine:
```bash
sudo xbps-install -S v4l-utils v4l2loopback gst-plugins-good1
sudo depmod -a
# remember current devices
ls /dev/video*
sudo modprobe v4l2loopback
# this should now show 1 more device - the loopback
ls /dev/video*
# start a gstreamer pipeline piping the live feed into the loopback device
# the flags probably could do with some adjustment...
gst-launch-1.0 souphttpsrc location="http://<mobile phone IP>:<configured port>/videofeed" \
        do-timestamp=true is-live=true ! \
        multipartdemux ! decodebin ! \
        videoflip method=horizontal-flip ! videoconvert ! v4l2sink device=/dev/video2
# better quality:
gst-launch-1.0 souphttpsrc location="http://<mobile phone IP>:<configured port>/videofeed" \
        do-timestamp=true is-live=true ! \
        multipartdemux ! decodebin ! \
        videoflip method=horizontal-flip ! videoconvert ! \
        videoscale ! videorate ! \
        video/x-raw,format=YUY2,width=1280,height=720,framerate=12/1 ! \
        v4l2sink device=/dev/video<N>
```

Once you've done that, you can simply select `/dev/video<N>` inside your application (VLC, for example)
