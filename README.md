Camera module that I have is like onboard connecting ov5647
OS : root@ysak:/home/ysak# lsb_release -a
No LSB modules are available.
Distributor ID:	Debian
Description:	Debian GNU/Linux 13 (trixie)
Release:	13
Codename:	trixie

Output for step 4:
root@ysak:/home/ysak# rpicam-hello --list-cameras
Available cameras
-----------------
0 : ov5647 [2592x1944 10-bit GBRG] (/base/soc/i2c0mux/i2c@1/ov5647@36)
    Modes: 'SGBRG10_CSI2P' : 640x480 [62.50 fps - (16, 0)/2560x1920 crop]
                             1296x972 [46.34 fps - (0, 0)/2592x1944 crop]
                             1920x1080 [32.81 fps - (348, 434)/1928x1080 crop]
                             2592x1944 [15.63 fps - (0, 0)/2592x1944 crop]

root@ysak:/home/ysak# rpicam-hello -n -t 3000
[1:08:28.256980533] [3697]  INFO Camera camera_manager.cpp:340 libcamera v0.7.2+rpt20260817
[1:08:28.289372705] [3700]  INFO RPI pipeline_base.cpp:1133 Using configuration file '/usr/share/libcamera/pipeline/rpi/vc4/rpi_apps.yaml'
[1:08:28.318987069] [3700]  INFO IPAProxy ipa_proxy.cpp:184 Using tuning file /usr/share/libcamera/ipa/rpi/vc4/ov5647.json
[1:08:28.326702252] [3700]  INFO Camera camera_manager.cpp:223 Adding camera '/base/soc/i2c0mux/i2c@1/ov5647@36' for pipeline handler rpi/vc4
[1:08:28.326822344] [3700]  INFO RPI vc4.cpp:445 Registered camera /base/soc/i2c0mux/i2c@1/ov5647@36 to Unicam device /dev/media0 and ISP device /dev/media2
Mode selection for 1296:972:12:P
    SGBRG10_CSI2P,640x480/0 - Score: 3296
    SGBRG10_CSI2P,1296x972/0 - Score: 1000
    SGBRG10_CSI2P,1920x1080/0 - Score: 1349.67
    SGBRG10_CSI2P,2592x1944/0 - Score: 1567
Stream configuration adjusted
[1:08:28.367223827] [3697]  INFO Camera camera.cpp:1216 configuring streams: (0) 1296x972-YUV420/sYCC (1) 1296x972-SGBRG10_CSI2P/RAW
[1:08:28.367651289] [3700]  INFO RPI vc4.cpp:620 Sensor: /base/soc/i2c0mux/i2c@1/ov5647@36 - Selected sensor format: 1296x972-SGBRG10_1X10/RAW - Selected unicam format: 1296x972-pGAA/RAW

Output for step 6:
root@ysak:/home/ysak# python3 surveillance/tools/phase1_camera_check.py --modes

=== Platform ===
[PASS] Board: Raspberry Pi 4 Model B Rev 1.5
[PASS] Operating system: Debian GNU/Linux 13 (trixie)
[PASS] CPU architecture: aarch64 (64-bit)
[INFO] Kernel: 6.18.50+rpt-rpi-v8
[PASS] Python: 3.13.5 (/usr/bin/python3)
[WARN] User: running as root
       -> Run as your normal user. The service will never run as root.

=== Boot configuration ===
[PASS] Legacy camera stack: not enabled (good)
[PASS] Camera auto-detect: camera_auto_detect=1 in /boot/firmware/config.txt

=== Power and temperature ===
[PASS] CPU temperature: 49.7 C
[PASS] Throttling status: throttled=0x0 (no under-voltage or throttling)

=== Hardware video encoders ===
[PASS] H.264 hardware encoder: bcm2835-codec-encode present
[PASS] JPEG hardware encoder: bcm2835-codec-encode_image present

=== Supporting tools (needed in later phases) ===
[PASS] rpicam-apps: /usr/bin/rpicam-hello
[PASS] FFmpeg: ffmpeg version 7.1.5-0+deb13u1+rpt2 Copyright (c) 2000-2026 the FFmpeg developers
[INFO] OpenCV: not installed yet (installed in Phase 6)

=== Camera (libcamera + Picamera2) ===
[PASS] Picamera2 import: 0.3.37
[PASS] Camera 0: model=ov5647 location=2 id=/base/soc/i2c0mux/i2c@1/ov5647@36
[INFO] Sensor: ov5647 pixel array (2592, 1944)
[INFO] Sensor mode: (640, 480) @ 62.5 fps, 10-bit SGBRG10_CSI2P
[INFO] Sensor mode: (1296, 972) @ 46.3 fps, 10-bit SGBRG10_CSI2P
[INFO] Sensor mode: (1920, 1080) @ 32.8 fps, 10-bit SGBRG10_CSI2P
[INFO] Sensor mode: (2592, 1944) @ 15.6 fps, 10-bit SGBRG10_CSI2P
[PASS] Configure dual-stream video mode: main 1920x1080 YUV420 + lores 640x360 YUV420
[INFO] Selected sensor mode: output_size=(1920, 1080) bit_depth=10
[PASS] Start camera
[PASS] Frame delivery: 45 frames, measured 15.0 fps (requested 15)
[INFO] Exposure: exposure=66543 us analogue_gain=8.00 lux=3.12 colour_temp=4110 K
[PASS] Low-resolution frame: 640x360 Y-plane mean brightness 26/255

=== Summary ===
PASS=18 WARN=1 FAIL=0 INFO=9
RESULT: CAMERA READY - Phase 1 complete.
