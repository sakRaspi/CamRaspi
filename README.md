Test A:
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 22:05:14 INFO Main: Surveillance recorder 0.4.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 22:05:15 INFO Clock: System clock is NTP-synchronised
[0:43:48.763422186] [2568]  INFO Camera camera_manager.cpp:340 libcamera v0.7.2+rpt20260817
[0:43:48.784545239] [2575]  INFO RPI pipeline_base.cpp:1133 Using configuration file '/usr/share/libcamera/pipeline/rpi/vc4/rpi_apps.yaml'
[0:43:48.808611818] [2575]  INFO IPAProxy ipa_proxy.cpp:184 Using tuning file /usr/share/libcamera/ipa/rpi/vc4/ov5647.json
[0:43:48.814847499] [2575]  INFO Camera camera_manager.cpp:223 Adding camera '/base/soc/i2c0mux/i2c@1/ov5647@36' for pipeline handler rpi/vc4
[0:43:48.814920239] [2575]  INFO RPI vc4.cpp:445 Registered camera /base/soc/i2c0mux/i2c@1/ov5647@36 to Unicam device /dev/media0 and ISP device /dev/media1
[0:43:48.822784970] [2568]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 640x480-SGBRG10_CSI2P/RAW
[0:43:48.823336542] [2575]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 640x480-SGBRG10_1X10/RAW - Selected unicam format: 640x480-pGAA/RAW
[0:43:48.837190269] [2568]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1296x972-SGBRG10_CSI2P/RAW
[0:43:48.837593527] [2575]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
[0:43:48.853165785] [2568]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1920x1080-SGBRG10_CSI2P/RAW
[0:43:48.853583487] [2575]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1920x1080-SGBRG10_1X10/RAW - Selected unicam format: 1920x1080-pGAA/RAW
[0:43:48.870677295] [2568]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 2592x1944-SGBRG10_CSI2P/RAW
[0:43:48.871077886] [2575]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 2592x1944-SGBRG10_1X10/RAW - Selected unicam format: 2592x1944-pGAA/RAW
[0:43:48.894951355] [2568]  INFO Camera camera.cpp:1216 configuring streams: (0) 1296x972-YUV420/Rec709 (1) 640x480-YUV420/Rec709 (2) 1296x972-SGBRG10_CSI2P/RAW
[0:43:48.895368112] [2575]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
2026-09-23 22:05:15 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 22:05:15 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-23 22:06:02 INFO Recorder: Segment completed: 2026-09-23/14-05-15Z.mp4 start=2026-09-23T14:05:15.810+00:00 duration=46.1s frames=690 size=7.2MB
2026-09-23 22:07:02 INFO Recorder: Segment completed: 2026-09-23/14-06-00Z.mp4 start=2026-09-23T14:06:01.957+00:00 duration=60.0s frames=900 size=9.4MB
2026-09-23 22:08:02 INFO Recorder: Segment completed: 2026-09-23/14-07-00Z.mp4 start=2026-09-23T14:07:01.942+00:00 duration=60.0s frames=900 size=9.4MB
2026-09-23 22:09:02 INFO Recorder: Segment completed: 2026-09-23/14-08-00Z.mp4 start=2026-09-23T14:08:01.932+00:00 duration=60.0s frames=900 size=9.4MB
^C2026-09-23 22:09:36 INFO Main: Received SIGINT, stopping
2026-09-23 22:09:36 INFO Recorder: Segment completed: 2026-09-23/14-09-00Z.mp4 start=2026-09-23T14:09:01.919+00:00 duration=34.3s frames=514 size=5.4MB
2026-09-23 22:09:36 INFO Recorder: Stopped recording
2026-09-23 22:09:36 INFO Main: Surveillance recorder stopped

ysak@ysak:~/surveillance $ python3 tools/phase4_check_segments.py 
Checking 6 segment(s) in /home/ysak/surveillance/recordings/2026-09-23 (segment length 60 s, 15 fps)

[FAIL] 14-02-07Z.mp4:   68.8 s, 1030 frames, 14.97 fps, 10.7 MB, 1.25 Mbit/s  <- 2 timestamp gap(s)
[FAIL] 14-05-15Z.mp4:   46.1 s, 690 frames, 14.96 fps, 7.2 MB, 1.25 Mbit/s  <- 2 timestamp gap(s)
[PASS] 14-06-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 14-07-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 14-08-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 14-09-00Z.mp4:   34.3 s, 514 frames, 15.00 fps, 5.4 MB, 1.26 Mbit/s

Continuity (a new segment should start where the previous one ended):
  [INFO] 14-02-07Z.mp4 -> 14-05-15Z.mp4: recorder stopped/restarted, 0:01:59 not in completed segments
  [PASS] 14-05-15Z.mp4 -> 14-06-00Z.mp4: -1.1 s
  [PASS] 14-06-00Z.mp4 -> 14-07-00Z.mp4: +0.0 s
  [PASS] 14-07-00Z.mp4 -> 14-08-00Z.mp4: +0.0 s
  [PASS] 14-08-00Z.mp4 -> 14-09-00Z.mp4: +0.0 s

FAIL=2 WARN=0 stopped-and-restarted=1
RESULT: PROBLEMS FOUND

Test B: 
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 22:15:45 INFO Main: Surveillance recorder 0.4.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 22:15:45 INFO Clock: System clock is NTP-synchronised
[0:54:19.454447841] [3087]  INFO Camera camera_manager.cpp:340 libcamera v0.7.2+rpt20260817
[0:54:19.475057651] [3094]  INFO RPI pipeline_base.cpp:1133 Using configuration file '/usr/share/libcamera/pipeline/rpi/vc4/rpi_apps.yaml'
[0:54:19.498134676] [3094]  INFO IPAProxy ipa_proxy.cpp:184 Using tuning file /usr/share/libcamera/ipa/rpi/vc4/ov5647.json
[0:54:19.505751098] [3094]  INFO Camera camera_manager.cpp:223 Adding camera '/base/soc/i2c0mux/i2c@1/ov5647@36' for pipeline handler rpi/vc4
[0:54:19.505835191] [3094]  INFO RPI vc4.cpp:445 Registered camera /base/soc/i2c0mux/i2c@1/ov5647@36 to Unicam device /dev/media0 and ISP device /dev/media1
[0:54:19.514497258] [3087]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 640x480-SGBRG10_CSI2P/RAW
[0:54:19.514964849] [3094]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 640x480-SGBRG10_1X10/RAW - Selected unicam format: 640x480-pGAA/RAW
[0:54:19.526207705] [3087]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1296x972-SGBRG10_CSI2P/RAW
[0:54:19.526724019] [3094]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
[0:54:19.539773221] [3087]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1920x1080-SGBRG10_CSI2P/RAW
[0:54:19.540305294] [3094]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1920x1080-SGBRG10_1X10/RAW - Selected unicam format: 1920x1080-pGAA/RAW
[0:54:19.555493527] [3087]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 2592x1944-SGBRG10_CSI2P/RAW
[0:54:19.555960563] [3094]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 2592x1944-SGBRG10_1X10/RAW - Selected unicam format: 2592x1944-pGAA/RAW
[0:54:19.580815805] [3087]  INFO Camera camera.cpp:1216 configuring streams: (0) 1296x972-YUV420/Rec709 (1) 640x480-YUV420/Rec709 (2) 1296x972-SGBRG10_CSI2P/RAW
[0:54:19.581218989] [3094]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
2026-09-23 22:15:45 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 22:15:45 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-23 22:16:00 INFO Recorder: Segment completed: 2026-09-23/14-15-46Z.mp4 start=2026-09-23T14:15:46.515+00:00 duration=14.2s frames=210 size=2.2MB
2026-09-23 22:17:00 INFO Recorder: Segment completed: 2026-09-23/14-16-00Z.mp4 start=2026-09-23T14:16:00.719+00:00 duration=60.0s frames=900 size=9.4MB
Killed

ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 22:17:39 INFO Main: Surveillance recorder 0.4.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 22:17:39 INFO Clock: System clock is NTP-synchronised
2026-09-23 22:17:39 WARNING Main: 1 unfinished segment(s) from a previous run (crash or power cut), newest: 14-17-00Z.mp4.partial. They are kept for recovery.
[0:56:13.351432494] [3144]  INFO Camera camera_manager.cpp:340 libcamera v0.7.2+rpt20260817
[0:56:13.372617359] [3151]  INFO RPI pipeline_base.cpp:1133 Using configuration file '/usr/share/libcamera/pipeline/rpi/vc4/rpi_apps.yaml'
[0:56:13.395088517] [3151]  INFO IPAProxy ipa_proxy.cpp:184 Using tuning file /usr/share/libcamera/ipa/rpi/vc4/ov5647.json
[0:56:13.402309923] [3151]  INFO Camera camera_manager.cpp:223 Adding camera '/base/soc/i2c0mux/i2c@1/ov5647@36' for pipeline handler rpi/vc4
[0:56:13.402410441] [3151]  INFO RPI vc4.cpp:445 Registered camera /base/soc/i2c0mux/i2c@1/ov5647@36 to Unicam device /dev/media0 and ISP device /dev/media1
[0:56:13.411601285] [3144]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 640x480-SGBRG10_CSI2P/RAW
[0:56:13.412156950] [3151]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 640x480-SGBRG10_1X10/RAW - Selected unicam format: 640x480-pGAA/RAW
[0:56:13.424479100] [3144]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1296x972-SGBRG10_CSI2P/RAW
[0:56:13.424941358] [3151]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
[0:56:13.438119450] [3144]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1920x1080-SGBRG10_CSI2P/RAW
[0:56:13.438553652] [3151]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1920x1080-SGBRG10_1X10/RAW - Selected unicam format: 1920x1080-pGAA/RAW
[0:56:13.454455385] [3144]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 2592x1944-SGBRG10_CSI2P/RAW
[0:56:13.454931087] [3151]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 2592x1944-SGBRG10_1X10/RAW - Selected unicam format: 2592x1944-pGAA/RAW
[0:56:13.480479903] [3144]  INFO Camera camera.cpp:1216 configuring streams: (0) 1296x972-YUV420/Rec709 (1) 640x480-YUV420/Rec709 (2) 1296x972-SGBRG10_CSI2P/RAW
[0:56:13.480894402] [3151]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
2026-09-23 22:17:39 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 22:17:39 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-23 22:18:01 INFO Recorder: Segment completed: 2026-09-23/14-17-40Z.mp4 start=2026-09-23T14:17:40.398+00:00 duration=20.1s frames=300 size=3.1MB
2026-09-23 22:19:00 INFO Recorder: Segment completed: 2026-09-23/14-18-00Z.mp4 start=2026-09-23T14:18:00.548+00:00 duration=60.0s frames=900 size=9.4MB
^C2026-09-23 22:19:47 INFO Main: Received SIGINT, stopping
2026-09-23 22:19:48 INFO Recorder: Segment completed: 2026-09-23/14-19-00Z.mp4 start=2026-09-23T14:19:00.539+00:00 duration=47.3s frames=710 size=7.4MB
2026-09-23 22:19:48 INFO Recorder: Stopped recording
2026-09-23 22:19:48 INFO Main: Surveillance recorder stopped
ysak@ysak:~/surveillance $ python3 tools/phase4_check_segments.py 
Checking 11 segment(s) in /home/ysak/surveillance/recordings/2026-09-23 (segment length 60 s, 15 fps)

[FAIL] 14-02-07Z.mp4:   68.8 s, 1030 frames, 14.97 fps, 10.7 MB, 1.25 Mbit/s  <- 2 timestamp gap(s)
[FAIL] 14-05-15Z.mp4:   46.1 s, 690 frames, 14.96 fps, 7.2 MB, 1.25 Mbit/s  <- 2 timestamp gap(s)
[PASS] 14-06-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 14-07-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 14-08-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 14-09-00Z.mp4:   34.3 s, 514 frames, 15.00 fps, 5.4 MB, 1.26 Mbit/s
[FAIL] 14-15-46Z.mp4:   14.2 s, 210 frames, 14.79 fps, 2.2 MB, 1.23 Mbit/s  <- 2 timestamp gap(s)
[PASS] 14-16-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[FAIL] 14-17-40Z.mp4:   20.1 s, 300 frames, 14.90 fps, 3.1 MB, 1.24 Mbit/s  <- 1 timestamp gap(s)
[PASS] 14-18-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 14-19-00Z.mp4:   47.3 s, 710 frames, 15.00 fps, 7.4 MB, 1.25 Mbit/s

Continuity (a new segment should start where the previous one ended):
  [INFO] 14-02-07Z.mp4 -> 14-05-15Z.mp4: recorder stopped/restarted, 0:01:59 not in completed segments
  [PASS] 14-05-15Z.mp4 -> 14-06-00Z.mp4: -1.1 s
  [PASS] 14-06-00Z.mp4 -> 14-07-00Z.mp4: +0.0 s
  [PASS] 14-07-00Z.mp4 -> 14-08-00Z.mp4: +0.0 s
  [PASS] 14-08-00Z.mp4 -> 14-09-00Z.mp4: +0.0 s
  [INFO] 14-09-00Z.mp4 -> 14-15-46Z.mp4: recorder stopped/restarted, 0:06:12 not in completed segments
  [PASS] 14-15-46Z.mp4 -> 14-16-00Z.mp4: -0.2 s
  [INFO] 14-16-00Z.mp4 -> 14-17-40Z.mp4: recorder stopped/restarted, 0:00:40 not in completed segments
  [PASS] 14-17-40Z.mp4 -> 14-18-00Z.mp4: -0.1 s
  [PASS] 14-18-00Z.mp4 -> 14-19-00Z.mp4: +0.0 s

Unfinished .partial files (the newest one is normal while the recorder is running):
  14-17-00Z.mp4.partial: 2.9 MB, 18.1 s playable

FAIL=4 WARN=0 stopped-and-restarted=3
RESULT: PROBLEMS FOUND

Test C: 
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 22:20:47 INFO Main: Surveillance recorder 0.4.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 22:20:47 INFO Clock: System clock is NTP-synchronised
2026-09-23 22:20:47 WARNING Main: 1 unfinished segment(s) from a previous run (crash or power cut), newest: 14-17-00Z.mp4.partial. They are kept for recovery.
[0:59:20.922839220] [3232]  INFO Camera camera_manager.cpp:340 libcamera v0.7.2+rpt20260817
[0:59:20.944531660] [3239]  INFO RPI pipeline_base.cpp:1133 Using configuration file '/usr/share/libcamera/pipeline/rpi/vc4/rpi_apps.yaml'
[0:59:20.968275890] [3239]  INFO IPAProxy ipa_proxy.cpp:184 Using tuning file /usr/share/libcamera/ipa/rpi/vc4/ov5647.json
[0:59:20.975142315] [3239]  INFO Camera camera_manager.cpp:223 Adding camera '/base/soc/i2c0mux/i2c@1/ov5647@36' for pipeline handler rpi/vc4
[0:59:20.975231889] [3239]  INFO RPI vc4.cpp:445 Registered camera /base/soc/i2c0mux/i2c@1/ov5647@36 to Unicam device /dev/media0 and ISP device /dev/media1
[0:59:20.977024792] [3232]  INFO Camera camera.cpp:1021 Pipeline handler in use by another process
2026-09-23 22:20:47 ERROR picamera2.picamera2: Camera __init__ sequence did not complete.
2026-09-23 22:20:47 ERROR Main: Could not start recording: cannot open camera 0 (in use by another program?): Camera __init__ sequence did not complete.. Retrying in 5 s
[0:59:25.981946134] [3232]  INFO Camera camera.cpp:1021 Pipeline handler in use by another process
2026-09-23 22:20:52 ERROR picamera2.picamera2: Camera __init__ sequence did not complete.
2026-09-23 22:20:52 ERROR Main: Could not start recording: cannot open camera 0 (in use by another program?): Camera __init__ sequence did not complete.. Retrying in 10 s
[0:59:35.989663090] [3232]  INFO Camera camera.cpp:1021 Pipeline handler in use by another process
2026-09-23 22:21:02 ERROR picamera2.picamera2: Camera __init__ sequence did not complete.
2026-09-23 22:21:02 ERROR Main: Could not start recording: cannot open camera 0 (in use by another program?): Camera __init__ sequence did not complete.. Retrying in 20 s
[0:59:56.011163264] [3232]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 640x480-SGBRG10_CSI2P/RAW
[0:59:56.012232243] [3239]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 640x480-SGBRG10_1X10/RAW - Selected unicam format: 640x480-pGAA/RAW
[0:59:56.042206937] [3232]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1296x972-SGBRG10_CSI2P/RAW
[0:59:56.044067654] [3239]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
[0:59:56.066632758] [3232]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 1920x1080-SGBRG10_CSI2P/RAW
[0:59:56.067198701] [3239]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1920x1080-SGBRG10_1X10/RAW - Selected unicam format: 1920x1080-pGAA/RAW
[0:59:56.092244873] [3232]  INFO Camera camera.cpp:1216 configuring streams: (0) 640x480-XBGR8888/sRGB (1) 2592x1944-SGBRG10_CSI2P/RAW
[0:59:56.092827556] [3239]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 2592x1944-SGBRG10_1X10/RAW - Selected unicam format: 2592x1944-pGAA/RAW
[0:59:56.127568941] [3232]  INFO Camera camera.cpp:1216 configuring streams: (0) 1296x972-YUV420/Rec709 (1) 640x480-YUV420/Rec709 (2) 1296x972-SGBRG10_CSI2P/RAW
[0:59:56.128152717] [3239]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW
2026-09-23 22:21:22 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 22:21:22 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
^C2026-09-23 22:21:31 INFO Main: Received SIGINT, stopping
2026-09-23 22:21:32 INFO Recorder: Segment completed: 2026-09-23/14-21-23Z.mp4 start=2026-09-23T14:21:23.050+00:00 duration=8.9s frames=131 size=1.4MB
2026-09-23 22:21:32 INFO Recorder: Stopped recording
2026-09-23 22:21:32 INFO Main: Surveillance recorder stopped
