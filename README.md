Step 4

ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-24 17:23:25 INFO Main: Surveillance recorder 0.10.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-24 17:23:25 INFO Clock: System clock is NTP-synchronised
2026-09-24 17:23:25 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 17:23:25 INFO StorageManager: Storage usage 1.22 GB / 45 GB, 48.4 GB free, 48 segments, oldest 2026-09-23 16:24 UTC
2026-09-24 17:23:27 INFO Database: Index matches the recordings folder (48 recordings)
2026-09-24 17:23:27 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-24 17:23:27 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 17:23:27 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 17:23:27 INFO LiveStream: Live view ready (up to 8 frames/s, medium quality, on demand)
2026-09-24 17:23:34 INFO MotionDetector: Motion started (area 1.1% of the frame)
2026-09-24 17:23:37 INFO LiveStream: Live view started (hardware JPEG, 7.5 frames/s)
2026-09-24 17:23:51 INFO MotionDetector: Motion ended after 6.8 s (peak area 15.8%)
2026-09-24 17:23:53 INFO MotionDetector: Motion started (area 1.7% of the frame)
2026-09-24 17:24:00 INFO Recorder: Segment completed: 2026-09-24/09-23-27Z.mp4 start=2026-09-24T09:23:27.977+00:00 duration=32.1s frames=481 size=5.1MB motion=yes
2026-09-24 17:24:08 INFO MotionDetector: Motion ended after 5.2 s (peak area 3.3%)
2026-09-24 17:24:10 INFO MotionDetector: Motion started (area 6.0% of the frame)
2026-09-24 17:24:24 INFO MotionDetector: Motion ended after 3.6 s (peak area 6.5%)
2026-09-24 17:24:24 INFO MotionDetector: Motion started (area 1.2% of the frame)
2026-09-24 17:24:37 INFO MotionDetector: Motion ended after 3.4 s (peak area 9.2%)
2026-09-24 17:25:00 INFO Recorder: Segment completed: 2026-09-24/09-24-00Z.mp4 start=2026-09-24T09:24:00.056+00:00 duration=60.0s frames=900 size=9.3MB motion=yes
2026-09-24 17:25:11 INFO MotionDetector: Motion started (area 7.2% of the frame)
2026-09-24 17:25:21 INFO MotionDetector: Motion ended after 0.6 s (peak area 7.2%)
2026-09-24 17:26:00 INFO Recorder: Segment completed: 2026-09-24/09-25-00Z.mp4 start=2026-09-24T09:25:00.046+00:00 duration=60.0s frames=900 size=9.4MB motion=yes
2026-09-24 17:26:11 INFO MotionDetector: Motion started (area 1.5% of the frame)
^C2026-09-24 17:26:23 INFO Main: Received SIGINT, stopping
2026-09-24 17:26:24 INFO LiveStream: Live view stopped (no viewers)
2026-09-24 17:26:24 INFO MotionDetector: Motion ended after 12.7 s (peak area 3.9%)
2026-09-24 17:26:24 INFO Recorder: Segment completed: 2026-09-24/09-26-00Z.mp4 start=2026-09-24T09:26:00.033+00:00 duration=24.1s frames=362 size=3.8MB motion=yes
2026-09-24 17:26:24 INFO Recorder: Stopped recording
2026-09-24 17:26:24 INFO Main: Surveillance recorder stopped
ysak@ysak:~/surveillance $ python3 tools/phase4_check_segments.py --last 3
Checking 3 segment(s) in /home/ysak/surveillance/recordings/2026-09-24 (segment length 60 s, 15 fps)

[PASS] 09-24-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.3 MB, 1.25 Mbit/s
[PASS] 09-25-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 09-26-00Z.mp4:   24.1 s, 362 frames, 15.00 fps, 3.8 MB, 1.27 Mbit/s

Continuity (a new segment should start where the previous one ended):
  [PASS] 09-24-00Z.mp4 -> 09-25-00Z.mp4: +0.0 s
  [PASS] 09-25-00Z.mp4 -> 09-26-00Z.mp4: +0.0 s

FAIL=0 WARN=0 stopped-and-restarted=0
RESULT: SEGMENTS OK

Step 5:
ysak@ysak:~ $ top -b -n 2 -d 60 -p "$(pgrep -f '^python3 -m app.main')" | grep python3
   7400 ysak      20   0 2376532 191040 127568 S   0.0   4.9   0:11.71 python3
   7400 ysak      20   0 2376532 191216 127568 S  22.7   4.9   0:25.35 python3

Overall the live view is perfect
