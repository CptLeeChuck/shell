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

# Send data from local webcam (first webcam in system = 0. List all devices with "gst-device-monitor-1.0")
gst-launch-1.0 -v avfvideosrc device-index=0 ! video/x-raw ! x264enc ! rtph264pay ! udpsink host=127.0.0.1 port=5000 

# Reduce latency were possible
gst-launch-1.0 -v avfvideosrc device-index=0 ! video/x-raw ! x264enc tune=zerolatency bitrate=500 speed-preset=ultrafast ! rtph264pay ! udpsink host=127.0.0.1 port=5000 

# Higher quality (±1MBit stream)
gst-launch-1.0 -v avfvideosrc device-index=0 ! video/x-raw ! x264enc tune=zerolatency bitrate=10000 speed-preset=ultrafast ! rtph264pay ! udpsink host=127.0.0.1 port=5000

# Audio send and receive
gst-launch-1.0 -v audiotestsrc freq=500 ! audioconvert ! audio/x-raw,channels=1,depth=16,width=16,rate=44100 ! rtpL16pay ! udpsink host=127.0.0.1 port=5000
gst-launch-1.0 -v udpsrc port=5000 ! "application/x-rtp,media=(string)audio, clock-rate=(int)44100, width=16, height=16, encoding-name=(string)L16, encoding-params=(string)1, channels=(int)1, channel-positions=(int)1, payload=(int)96" ! rtpL16depay ! audioconvert ! autoaudiosink

# audio send and receive from connected USB microphone with OPUS codec
# Sender
gst-launch-1.0 osxaudiosrc device=43 ! audioconvert ! audioresample ! opusenc ! rtpopuspay ! udpsink host=127.0.0.1 port=5000
# Receiver
gst-launch-1.0 udpsrc caps="application/x-rtp,media=(string)audio,clock-rate=(int)48000,encoding-name=(string)X-GST-OPUS-DRAFT-SPITTKA-00" port=5000 ! rtpopusdepay ! opusdec plc=true ! autoaudiosink
# Receiver with jitter buffer
gst-launch-1.0 udpsrc caps="application/x-rtp,media=(string)audio,clock-rate=(int)48000,encoding-name=(string)X-GST-OPUS-DRAFT-SPITTKA-00" port=5000 ! rtpjitterbuffer latency=200 ! rtpopusdepay ! opusdec plc=true ! autoaudiosink




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

| en                         | de                                 | Default  | Adjusted  |
| -------------------------- | ---------------------------------- | -------- | --------- |
| File caching (ms)          | Datei-Cachewert (ms)               | 300      | 0         |
| Live capture caching (ms)  | Cachewert für Liveaufnahmen (ms)   | 300      | 0         |
| Disc caching (ms)          | Disk-Cachewert (ms)                | 300      | 0         |
| Network caching (ms)       | Cachewert für das Netzwerk (ms)    | 1000     | 0         |


6. Open SDP file in VLC
