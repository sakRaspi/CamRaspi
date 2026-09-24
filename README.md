ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-24 18:09:51 INFO Main: Surveillance recorder 0.11.1 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-24 18:09:51 INFO Clock: System clock is NTP-synchronised
2026-09-24 18:09:51 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 18:09:51 INFO StorageManager: Storage usage 1.30 GB / 45 GB, 48.4 GB free, 54 segments, oldest 2026-09-23 16:24 UTC
2026-09-24 18:09:53 INFO Database: Index matches the recordings folder (54 recordings)
2026-09-24 18:09:53 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-24 18:09:53 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (300 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 18:09:53 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 18:09:53 INFO LiveStream: Live view ready (up to 8 frames/s, medium quality, on demand)
2026-09-24 18:09:57 INFO MotionDetector: Motion started (area 1.0% of the frame)
2026-09-24 18:10:00 INFO Recorder: Segment completed: 2026-09-24/10-09-54Z.mp4 start=2026-09-24T10:09:54.253+00:00 duration=5.8s frames=87 size=0.9MB motion=yes
2026-09-24 18:10:14 INFO LiveStream: Live view started (hardware JPEG, 7.5 frames/s)
2026-09-24 18:10:21 INFO LiveStream: Live view stopped (no viewers)
2026-09-24 18:11:41 INFO LiveStream: Live view started (hardware JPEG, 7.5 frames/s)
2026-09-24 18:11:53 INFO LiveStream: Live view stopped (no viewers)
2026-09-24 18:12:06 INFO Main: Settings changed (camera.tuning_file); restarting the recording pipeline
2026-09-24 18:12:06 INFO MotionDetector: Motion ended after 129.2 s (peak area 56.5%)
2026-09-24 18:12:07 INFO Recorder: Segment completed: 2026-09-24/10-10-00Z.mp4 start=2026-09-24T10:10:00.054+00:00 duration=126.5s frames=1898 size=19.8MB motion=yes
2026-09-24 18:12:07 INFO Recorder: Stopped recording
2026-09-24 18:12:07 INFO Main: Recorder restarting with the new settings
2026-09-24 18:12:07 INFO Clock: System clock is NTP-synchronised
2026-09-24 18:12:07 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 18:12:07 INFO StorageManager: Storage usage 1.32 GB / 45 GB, 48.3 GB free, 56 segments, oldest 2026-09-23 16:24 UTC
2026-09-24 18:12:07 INFO Database: Index matches the recordings folder (56 recordings)
2026-09-24 18:12:07 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972, tuning ov5647_noir.json
2026-09-24 18:12:07 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (300 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 18:12:07 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 18:12:07 INFO LiveStream: Live view ready (up to 8 frames/s, medium quality, on demand)
2026-09-24 18:12:11 INFO MotionDetector: Motion started (area 11.2% of the frame)
2026-09-24 18:12:13 INFO LiveStream: Live view started (hardware JPEG, 7.5 frames/s)
2026-09-24 18:12:20 INFO LiveStream: Live view stopped (no viewers)
2026-09-24 18:12:42 INFO Main: Settings changed (camera.tuning_file); restarting the recording pipeline
2026-09-24 18:12:42 INFO MotionDetector: Motion ended after 31.0 s (peak area 54.1%)
2026-09-24 18:12:42 INFO Recorder: Segment completed: 2026-09-24/10-12-08Z.mp4 start=2026-09-24T10:12:08.068+00:00 duration=34.2s frames=512 size=5.4MB motion=yes
2026-09-24 18:12:42 INFO Recorder: Stopped recording
2026-09-24 18:12:42 INFO Main: Recorder restarting with the new settings
2026-09-24 18:12:43 INFO Clock: System clock is NTP-synchronised
2026-09-24 18:12:43 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 18:12:43 INFO StorageManager: Storage usage 1.33 GB / 45 GB, 48.3 GB free, 57 segments, oldest 2026-09-23 16:24 UTC
[4:14:18.214092976] [9332]  WARN RPiController controller.cpp:100 Failed to open tuning file '/tmp/tmpk9zgd2q8'
[4:14:18.214184717] [9332] ERROR IPARPI ipa_base.cpp:163 Failed to load tuning data file /tmp/tmpk9zgd2q8
[4:14:18.214254717] [9332] ERROR RPI pipeline_base.cpp:830 Failed to load a suitable IPA library
[4:14:18.214274161] [9332] ERROR RPI vc4.cpp:219 Failed to register camera ov5647 10-0036: -22
2026-09-24 18:12:43 INFO Database: Index matches the recordings folder (57 recordings)
2026-09-24 18:12:43 ERROR Main: Could not start recording: camera 0 not detected (0 camera(s) found). Retrying in 5 s
2026-09-24 18:12:48 ERROR Main: Could not start recording: camera 0 not detected (0 camera(s) found). Retrying in 10 s
2026-09-24 18:12:57 INFO Main: Settings changed (camera.tuning_file); restarting the recording pipeline
2026-09-24 18:12:57 INFO Main: Recorder restarting with the new settings
2026-09-24 18:12:57 INFO Clock: System clock is NTP-synchronised
2026-09-24 18:12:57 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 18:12:58 INFO StorageManager: Storage usage 1.33 GB / 45 GB, 48.3 GB free, 57 segments, oldest 2026-09-23 16:24 UTC
2026-09-24 18:12:58 ERROR Main: Could not start recording: camera 0 not detected (0 camera(s) found). Retrying in 5 s
2026-09-24 18:12:58 INFO Database: Index matches the recordings folder (57 recordings)
2026-09-24 18:13:03 ERROR Main: Could not start recording: camera 0 not detected (0 camera(s) found). Retrying in 10 s
2026-09-24 18:13:13 ERROR Main: Could not start recording: camera 0 not detected (0 camera(s) found). Retrying in 20 s
2026-09-24 18:13:33 ERROR Main: Could not start recording: camera 0 not detected (0 camera(s) found). Retrying in 40 s
2026-09-24 18:14:13 ERROR Main: Could not start recording: camera 0 not detected (0 camera(s) found). Retrying in 60 s

Above is step 3, I test every dropdown option, and suddenly it only have the option for default for this camera. and I realized I need to restart the service then the camera can be use again. And same thing happen, after i select all the option, then saved settings, it will only left with default for this camera and nothing show up in the Live view.

Step 4 and 5 works fine.
