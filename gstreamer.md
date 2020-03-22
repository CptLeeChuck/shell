# Using gstreamer under macOS (Catalina)

## Installation
With support of brew.sh install the following packages. (gst-rtsp-server might be not required)

```sh
brew install gstreamer
brew install gst-libav
brew install gst-plugins-good
brew install gst-plugins-base
brew install gst-rtsp-server
brew install gst-plugins-ugly
brew install gst-plugins-bad
brew install cask vlc

```

## Launch (send)


```sh
# Send a audio test stream and playback local
gst-launch-1.0 audiotestsrc ! audioconvert ! autoaudiosink
gst-launch-1.0 audiotestsrc freq=500 ! audioconvert ! autoaudiosink

# Send test picture/video via UDP in H264 and verbose output
gst-launch-1.0 -v videotestsrc ! x264enc ! rtph264pay ! udpsink host=127.0.0.1 port=5000

# Send test picture with custom size
gst-launch-1.0 -v videotestsrc ! video/x-raw,width=1280,height=720 ! x264enc ! rtph264pay ! udpsink host=127.0.0.1 port=5000

# Adjust frames per second to be sent
gst-launch-1.0 -v videotestsrc ! video/x-raw,width=1280,height=720,framerate=3/1 ! x264enc ! rtph264pay ! udpsink host=127.0.0.1 port=5000

# Send data from local webcam
gst-launch-1.0 -v autovideosrc device=/dev/video0 ! video/x-raw ! x264enc ! rtph264pay ! udpsink host=127.0.0.1 port=5000 

# Reduce latency were possible
gst-launch-1.0 -v autovideosrc device=/dev/video0 ! video/x-raw ! x264enc tune=zerolatency bitrate=500 speed-preset=ultrafast ! rtph264pay ! udpsink host=127.0.0.1 port=5000 

# Higher quality (±1MBit stream)
gst-launch-1.0 -v autovideosrc device=/dev/video0 ! video/x-raw ! x264enc tune=zerolatency bitrate=10000 speed-preset=ultrafast ! rtph264pay ! udpsink host=127.0.0.1 port=5000 
```



## Receive data with VLC

1. Prepare SDP file
```
v=0
m=video 5000 RTP/AVP 96
c=IN IP4 127.0.0.1
a=rtpmap:96 H264/90000
```

2. Open VLC Setting
3. Show All 🇩🇪Alle einblenden
4. Input / Codecs 🇩🇪Eingang/Codecs
5. Adjust cache settings to avoid delay (optional) 
  * Default value "File caching (ms)"  *300* (🇩🇪"Datei-Cachewert (ms)")
  * Default value "Live capture caching (ms)" "Datei-Cachewert (ms)" *300* (🇩🇪"Cachewert für Liveaufnahmen (ms)")
  * Default value "Disc caching (ms)" *300* (🇩🇪"Disk-Cachewert (ms)")
  * Default value "Network caching (ms)" *1000* (🇩🇪"Cachewert für das Netzwerk (ms)")
6. Open SDP file in VLC
