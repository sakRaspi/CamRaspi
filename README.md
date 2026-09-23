Step 0:
ysak@ysak:~/surveillance $ python3 tools/set_setting.py storage.min_free_gb 120
storage.min_free_gb: 2.0 -> 120  (restart the recorder to apply)
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-24 00:15:55 INFO Main: Surveillance recorder 0.6.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-24 00:15:56 INFO Clock: System clock is NTP-synchronised
2026-09-24 00:15:56 WARNING StorageManager: Maximum storage 45 GB does not fit on this disk while keeping 120 GB free (room for about 0.00 GB); the free-space limit will apply first
2026-09-24 00:15:56 ERROR StorageManager: Storage critical: only 49.71 GB free and nothing left to delete; recording is PAUSED until more space is available
2026-09-24 00:15:56 INFO StorageManager: Storage usage 0.00 GB / 45 GB, 49.7 GB free, 0 segments, oldest none
2026-09-24 00:15:57 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-24 00:15:57 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (300 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 00:15:57 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 00:15:58 ERROR Recorder: Recording paused: storage is critically full
2026-09-24 00:15:59 INFO MotionDetector: Motion started (area 6.3% of the frame)
^C2026-09-24 00:17:20 INFO Main: Received SIGINT, stopping
2026-09-24 00:17:20 INFO MotionDetector: Motion ended after 80.8 s (peak area 57.4%)
2026-09-24 00:17:21 INFO Recorder: Stopped recording
2026-09-24 00:17:21 INFO Main: Surveillance recorder stopped

Summary for Step 1 - 3 : Overall is good

Step 4:
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-24 00:29:40 INFO Main: Surveillance recorder 0.6.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-24 00:29:41 INFO Clock: System clock is NTP-synchronised
2026-09-24 00:29:41 INFO StorageManager: Storage usage 0.01 GB / 45 GB, 49.7 GB free, 1 segments, oldest 2026-09-23 16:24 UTC
2026-09-24 00:29:42 INFO Camera: Opened ov5647: main 12
96x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-24 00:29:42 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (300 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 00:29:42 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 00:29:46 INFO MotionDetector: Motion started (area 0.9% of the frame)
2026-09-24 00:30:00 INFO Recorder: Segment completed: 2026-09-23/16-29-43Z.mp4 start=2026-09-23T16:29:43.607+00:00 duration=16.5s frames=247 size=2.6MB motion=yes
2026-09-24 00:30:01 INFO MotionDetector: Motion ended after 5.8 s (peak area 1.2%)
2026-09-24 00:30:02 INFO MotionDetector: Motion started (area 9.8% of the frame)
^C2026-09-24 00:30:26 INFO Main: Received SIGINT, stopping
2026-09-24 00:30:26 INFO MotionDetector: Motion ended after 24.8 s (peak area 13.8%)
2026-09-24 00:30:27 INFO Recorder: Segment completed: 2026-09-23/16-30-00Z.mp4 start=2026-09-23T16:30:00.089+00:00 duration=26.7s frames=401 size=4.2MB motion=yes
2026-09-24 00:30:27 INFO Recorder: Stopped recording
2026-09-24 00:30:27 INFO Main: Surveillance recorder stopped

Step 5: 
