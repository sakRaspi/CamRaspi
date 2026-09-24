Step 2:
First window:
ysak@ysak:~/surveillance $ python3 tools/set_setting.py recording.segment_seconds 60
recording.segment_seconds: 300 -> 60  (restart the recorder to apply)
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-24 14:03:52 INFO Main: Surveillance recorder 0.7.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-24 14:03:53 INFO Clock: System clock is NTP-synchronised
2026-09-24 14:03:53 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 14:03:57 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-24 14:03:57 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 14:03:57 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 14:03:58 INFO Database: Index reconciled with the recordings folder: 3 added, 0 removed
2026-09-24 14:03:58 INFO Database: Database backup written: surveillance-20260924T060358Z.db
2026-09-24 14:04:00 INFO Recorder: Segment completed: 2026-09-24/06-03-58Z.mp4 start=2026-09-24T06:03:58.557+00:00 duration=1.5s frames=22 size=0.2MB motion=no
2026-09-24 14:04:18 INFO MotionDetector: Motion started (area 11.8% of the frame)
2026-09-24 14:04:36 INFO MotionDetector: Motion ended after 8.2 s (peak area 43.1%)
2026-09-24 14:05:00 INFO Recorder: Segment completed: 2026-09-24/06-04-00Z.mp4 start=2026-09-24T06:04:00.036+00:00 duration=60.0s frames=900 size=9.4MB motion=yes
2026-09-24 14:05:02 INFO MotionDetector: Motion started (area 0.7% of the frame)
2026-09-24 14:05:17 INFO MotionDetector: Motion ended after 5.2 s (peak area 0.7%)
2026-09-24 14:05:25 INFO MotionDetector: Motion started (area 13.7% of the frame)
2026-09-24 14:05:40 INFO MotionDetector: Motion ended after 5.2 s (peak area 13.7%)
2026-09-24 14:06:00 INFO Recorder: Segment completed: 2026-09-24/06-05-00Z.mp4 start=2026-09-24T06:05:00.017+00:00 duration=60.1s frames=901 size=9.4MB motion=yes
^C2026-09-24 14:06:01 INFO Main: Received SIGINT, stopping
2026-09-24 14:06:02 INFO Recorder: Segment completed: 2026-09-24/06-06-00Z.mp4 start=2026-09-24T06:06:00.083+00:00 duration=1.9s frames=29 size=0.3MB motion=no
2026-09-24 14:06:02 INFO Recorder: Stopped recording
2026-09-24 14:06:02 INFO Main: Surveillance recorder stopped

Second window:
ysak@ysak:~/surveillance $ python3 tools/phase7_db_report.py
Database        : /home/ysak/surveillance/database/surveillance.db  (integrity: ok)
Recordings      : 5 (1 with motion, 0 recovered), 0.02 GB, 0.0 h
Covering        : 2026-09-24 00:24 to 2026-09-24 14:04 (local time)
Motion events   : 3

24 September 2026 (local time)   ─ recording   █ motion   · nothing recorded
00:00 ──··························█··················· 24:00

Motion events (3):
  14:04:17  Motion detected  8.2 s, peak 43.1%  ->  2026-09-24/06-04-00Z.mp4
  14:05:02  Motion detected  5.2 s, peak 0.7%  ->  footage deleted / not yet indexed
  14:05:24  Motion detected  5.2 s, peak 13.7%  ->  footage deleted / not yet indexed

Recordings (5):
  00:24:10 - 00:24:43    33.9 s     5.3 MB  2026-09-23/16-24-10Z.mp4
  00:29:43 - 00:29:59    16.4 s     2.6 MB  2026-09-23/16-29-43Z.mp4
  00:30:00 - 00:30:26    26.7 s     4.2 MB  2026-09-23/16-30-00Z.mp4
  14:03:58 - 14:04:00     1.5 s     0.2 MB  2026-09-24/06-03-58Z.mp4
  14:04:00 - 14:05:00    60.0 s     9.4 MB  2026-09-24/06-04-00Z.mp4  motion
ysak@ysak:~/surveillance $ python3 tools/phase7_db_report.py
Database        : /home/ysak/surveillance/database/surveillance.db  (integrity: ok)
Recordings      : 7 (2 with motion, 0 recovered), 0.03 GB, 0.1 h
Covering        : 2026-09-24 00:24 to 2026-09-24 14:06 (local time)
Motion events   : 3

24 September 2026 (local time)   ─ recording   █ motion   · nothing recorded
00:00 ──··························█··················· 24:00

Motion events (3):
  14:04:17  Motion detected  8.2 s, peak 43.1%  ->  2026-09-24/06-04-00Z.mp4
  14:05:02  Motion detected  5.2 s, peak 0.7%  ->  2026-09-24/06-05-00Z.mp4
  14:05:24  Motion detected  5.2 s, peak 13.7%  ->  2026-09-24/06-05-00Z.mp4

Recordings (7):
  00:24:10 - 00:24:43    33.9 s     5.3 MB  2026-09-23/16-24-10Z.mp4
  00:29:43 - 00:29:59    16.4 s     2.6 MB  2026-09-23/16-29-43Z.mp4
  00:30:00 - 00:30:26    26.7 s     4.2 MB  2026-09-23/16-30-00Z.mp4
  14:03:58 - 14:04:00     1.5 s     0.2 MB  2026-09-24/06-03-58Z.mp4
  14:04:00 - 14:05:00    60.0 s     9.4 MB  2026-09-24/06-04-00Z.mp4  motion
  14:05:00 - 14:06:00    60.1 s     9.4 MB  2026-09-24/06-05-00Z.mp4  motion
  14:06:00 - 14:06:02     1.9 s     0.3 MB  2026-09-24/06-06-00Z.mp4

Step 4:
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-24 14:08:07 INFO Main: Surveillance recorder 0.7.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-24 14:08:07 INFO Clock: System clock is NTP-synchronised
2026-09-24 14:08:07 WARNING Database: Closed 1 motion event(s) left open by a crash or power cut
2026-09-24 14:08:07 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 14:08:08 INFO Database: Index matches the recordings folder (7 recordings)
2026-09-24 14:08:08 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-24 14:08:08 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 14:08:08 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 14:08:15 INFO MotionDetector: Motion started (area 0.5% of the frame)
2026-09-24 14:08:37 INFO StorageManager: Storage usage 0.04 GB / 45 GB, 49.6 GB free, 7 segments, oldest 2026-09-23 16:24 UTC
2026-09-24 14:08:41 INFO MotionDetector: Motion ended after 16.6 s (peak area 49.3%)
2026-09-24 14:08:51 INFO MotionDetector: Motion started (area 25.4% of the frame)
2026-09-24 14:09:00 INFO Recorder: Segment completed: 2026-09-24/06-08-09Z.mp4 start=2026-09-24T06:08:09.584+00:00 duration=50.5s frames=757 size=7.9MB motion=yes
2026-09-24 14:09:01 WARNING StorageManager: Recovered unfinished segment 2026-09-24/06-07-45Z.mp4.partial -> 06-07-45Z.recovered.mp4: 9.9 s playable, removed 282 KiB of incomplete data
2026-09-24 14:09:09 INFO MotionDetector: Motion ended after 8.4 s (peak area 25.4%)
^C2026-09-24 14:09:09 INFO Main: Received SIGINT, stopping
2026-09-24 14:09:10 INFO Recorder: Segment completed: 2026-09-24/06-09-00Z.mp4 start=2026-09-24T06:09:00.056+00:00 duration=10.0s frames=150 size=1.5MB motion=no
2026-09-24 14:09:10 INFO Recorder: Stopped recording
2026-09-24 14:09:10 INFO Main: Surveillance recorder stopped
ysak@ysak:~/surveillance $ python3 tools/phase7_db_report.py
Database        : /home/ysak/surveillance/database/surveillance.db  (integrity: ok)
Recordings      : 10 (4 with motion, 1 recovered), 0.04 GB, 0.1 h
Covering        : 2026-09-24 00:24 to 2026-09-24 14:09 (local time)
Motion events   : 6

24 September 2026 (local time)   ─ recording   █ motion   · nothing recorded
00:00 ──··························█··················· 24:00

Motion events (6):
  14:04:17  Motion detected  8.2 s, peak 43.1%  ->  2026-09-24/06-04-00Z.mp4
  14:05:02  Motion detected  5.2 s, peak 0.7%  ->  2026-09-24/06-05-00Z.mp4
  14:05:24  Motion detected  5.2 s, peak 13.7%  ->  2026-09-24/06-05-00Z.mp4
  14:07:48  Motion detected  0.0 s, peak 27.3%  ->  2026-09-24/06-07-45Z.recovered.mp4
  14:08:14  Motion detected  16.6 s, peak 49.3%  ->  2026-09-24/06-08-09Z.mp4
  14:08:50  Motion detected  8.4 s, peak 25.4%  ->  2026-09-24/06-08-09Z.mp4

Recordings (10):
  00:24:10 - 00:24:43    33.9 s     5.3 MB  2026-09-23/16-24-10Z.mp4
  00:29:43 - 00:29:59    16.4 s     2.6 MB  2026-09-23/16-29-43Z.mp4
  00:30:00 - 00:30:26    26.7 s     4.2 MB  2026-09-23/16-30-00Z.mp4
  14:03:58 - 14:04:00     1.5 s     0.2 MB  2026-09-24/06-03-58Z.mp4
  14:04:00 - 14:05:00    60.0 s     9.4 MB  2026-09-24/06-04-00Z.mp4  motion
  14:05:00 - 14:06:00    60.1 s     9.4 MB  2026-09-24/06-05-00Z.mp4  motion
  14:06:00 - 14:06:02     1.9 s     0.3 MB  2026-09-24/06-06-00Z.mp4
  14:07:45 - 14:07:54     9.9 s     1.5 MB  2026-09-24/06-07-45Z.recovered.mp4  motion  recovered
  14:08:09 - 14:09:00    50.5 s     7.9 MB  2026-09-24/06-08-09Z.mp4  motion
  14:09:00 - 14:09:10    10.0 s     1.5 MB  2026-09-24/06-09-00Z.mp4

Step 5:
ysak@ysak:~/surveillance $ dd if=/dev/urandom of=database/surveillance.db bs=1 seek=100 count=4000 conv=notrunc
4000+0 records in
4000+0 records out
4000 bytes (4.0 kB, 3.9 KiB) copied, 0.0106189 s, 377 kB/s
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-24 14:10:20 INFO Main: Surveillance recorder 0.7.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-24 14:10:20 INFO Clock: System clock is NTP-synchronised
2026-09-24 14:10:20 ERROR Database: Database failed its integrity check; moved aside as surveillance.db.corrupt-20260924T061020Z
2026-09-24 14:10:20 WARNING Database: Restored the database from backup surveillance-20260924T060358Z.db; the index will be rebuilt from the files
2026-09-24 14:10:20 INFO Database: Database ready: /home/ysak/surveillance/database/surveillance.db
2026-09-24 14:10:20 INFO StorageManager: Storage usage 0.04 GB / 45 GB, 49.6 GB free, 10 segments, oldest 2026-09-23 16:24 UTC
2026-09-24 14:10:22 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-24 14:10:22 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-24 14:10:22 INFO MotionDetector: Motion detection on: sensitivity 70 (pixel threshold 28), min area 0.50%, cooldown 10 s, 5 analysed frames/s
2026-09-24 14:10:25 INFO Database: Index reconciled with the recordings folder: 7 added, 0 removed
^C2026-09-24 14:10:30 INFO Main: Received SIGINT, stopping
2026-09-24 14:10:31 INFO Recorder: Segment completed: 2026-09-24/06-10-23Z.mp4 start=2026-09-24T06:10:23.399+00:00 duration=7.5s frames=113 size=1.2MB motion=no
2026-09-24 14:10:31 INFO Recorder: Stopped recording
2026-09-24 14:10:31 INFO Main: Surveillance recorder stopped
ysak@ysak:~/surveillance $ python3 tools/phase7_db_report.py
Database        : /home/ysak/surveillance/database/surveillance.db  (integrity: ok)
Recordings      : 11 (0 with motion, 1 recovered), 0.04 GB, 0.1 h
Covering        : 2026-09-24 00:24 to 2026-09-24 14:10 (local time)
Motion events   : 0

24 September 2026 (local time)   ─ recording   █ motion   · nothing recorded
00:00 ──··························─··················· 24:00

Motion events (0):

Recordings (11):
  00:24:10 - 00:24:43    33.9 s     5.3 MB  2026-09-23/16-24-10Z.mp4
  00:29:43 - 00:29:59    16.4 s     2.6 MB  2026-09-23/16-29-43Z.mp4
  00:30:00 - 00:30:26    26.7 s     4.2 MB  2026-09-23/16-30-00Z.mp4
  14:03:58 - 14:03:59     1.4 s     0.2 MB  2026-09-24/06-03-58Z.mp4
  14:04:00 - 14:04:59    60.0 s     9.4 MB  2026-09-24/06-04-00Z.mp4
  14:05:00 - 14:06:00    60.0 s     9.4 MB  2026-09-24/06-05-00Z.mp4
  14:06:00 - 14:06:01     1.9 s     0.3 MB  2026-09-24/06-06-00Z.mp4
  14:07:45 - 14:07:54    10.0 s     1.5 MB  2026-09-24/06-07-45Z.recovered.mp4  recovered
  14:08:09 - 14:08:59    50.4 s     7.9 MB  2026-09-24/06-08-09Z.mp4
  14:09:00 - 14:09:09    10.0 s     1.5 MB  2026-09-24/06-09-00Z.mp4
  14:10:23 - 14:10:30     7.5 s     1.2 MB  2026-09-24/06-10-23Z.mp4

Step 6:
ysak@ysak:~/surveillance $ top -b -n 2 -d 60 -p "$(pgrep -f '^python3 -m app.main')" | grep python3
   2720 ysak      20   0 2051120 191736 127708 S  27.3   4.9   0:10.50 python3
   2720 ysak      20   0 2051120 191580 127708 S  21.8   4.9   0:23.57 python3


