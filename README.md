Test A:
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 23:26:01 INFO Main: Surveillance recorder 0.5.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 23:26:01 INFO Clock: System clock is NTP-synchronised
2026-09-23 23:26:01 INFO StorageManager: Storage usage 0.00 GB / 45 GB, 50.2 GB free, 0 segments, oldest none
2026-09-23 23:26:01 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 23:26:01 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-23 23:27:00 INFO Recorder: Segment completed: 2026-09-23/15-26-00Z.mp4 start=2026-09-23T15:26:01.950+00:00 duration=58.1s frames=872 size=9.1MB
Killed
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 23:27:32 INFO Main: Surveillance recorder 0.5.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 23:27:32 INFO Clock: System clock is NTP-synchronised
2026-09-23 23:27:32 INFO StorageManager: Storage usage 0.01 GB / 45 GB, 50.2 GB free, 1 segments, oldest 2026-09-23 15:26 UTC
2026-09-23 23:27:33 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 23:27:33 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-23 23:28:00 INFO Recorder: Segment completed: 2026-09-23/15-27-33Z.mp4 start=2026-09-23T15:27:33.762+00:00 duration=26.3s frames=394 size=4.1MB
2026-09-23 23:28:30 WARNING StorageManager: Recovered unfinished segment 2026-09-23/15-27-00Z.mp4.partial -> 15-27-00Z.recovered.mp4: 23.9 s playable, removed 204 KiB of incomplete data
^C2026-09-23 23:28:37 INFO Main: Received SIGINT, stopping
2026-09-23 23:28:38 INFO Recorder: Segment completed: 2026-09-23/15-28-00Z.mp4 start=2026-09-23T15:28:00.043+00:00 duration=37.6s frames=564 size=5.9MB
2026-09-23 23:28:38 INFO Recorder: Stopped recording
2026-09-23 23:28:38 INFO Main: Surveillance recorder stopped

Test B:
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 23:30:59 INFO Main: Surveillance recorder 0.5.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 23:30:59 INFO Clock: System clock is NTP-synchronised
2026-09-23 23:30:59 INFO StorageManager: Storage usage 0.02 GB / 0.03 GB, 50.2 GB free, 4 segments, oldest 2026-09-23 15:26 UTC
2026-09-23 23:30:59 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 23:30:59 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
2026-09-23 23:31:59 INFO StorageManager: Deleted 2026-09-23/15-26-00Z.mp4 (9.1 MB): over the 0.03 GB limit
2026-09-23 23:32:00 INFO Recorder: Segment completed: 2026-09-23/15-31-00Z.mp4 start=2026-09-23T15:31:00.131+00:00 duration=59.9s frames=899 size=9.4MB
2026-09-23 23:33:00 INFO Recorder: Segment completed: 2026-09-23/15-32-00Z.mp4 start=2026-09-23T15:32:00.055+00:00 duration=60.0s frames=900 size=9.4MB
2026-09-23 23:33:00 INFO StorageManager: Deleted 2026-09-23/15-27-00Z.recovered.mp4 (3.7 MB): over the 0.03 GB limit
^C2026-09-23 23:33:12 INFO Main: Received SIGINT, stopping
2026-09-23 23:33:12 INFO Recorder: Segment completed: 2026-09-23/15-33-00Z.mp4 start=2026-09-23T15:33:00.042+00:00 duration=12.3s frames=184 size=2.0MB
2026-09-23 23:33:12 INFO StorageManager: Deleted 2026-09-23/15-27-33Z.mp4 (4.1 MB): over the 0.03 GB limit
2026-09-23 23:33:13 INFO Recorder: Stopped recording
2026-09-23 23:33:13 INFO Main: Surveillance recorder stopped
ysak@ysak:~/surveillance $ python3 tools/phase5_storage_report.py 
Recordings folder : /home/ysak/surveillance/recordings
Stored on         : /dev/mmcblk0p2 (ext4), mounted at /
Disk              : 60.9 GB total, 50.2 GB free
Limits            : max 0.03 GB, keep 2 GB free, max age off, required mount none
Recordings        : 0.03 GB in 4 segments (0 recovered, 0 unfinished)
Oldest / newest   : 2026-09-23 15:28 UTC (2026-09-23 23:28 local) / 2026-09-23 15:33 UTC (2026-09-23 23:33 local)
Average bitrate   : 1.09 Mbit/s (last 3 full segments)
Estimated history : 0.03 GB usable = about 0.1 h (0.0 days)
Would delete now  : 0 segment(s)
ysak@ysak:~/surveillance $ python3 tools/phase4_check_segments.py
Checking 4 segment(s) in /home/ysak/surveillance/recordings/2026-09-23 (segment length 60 s, 15 fps)

[PASS] 15-28-00Z.mp4:   37.6 s, 564 frames, 15.00 fps, 5.9 MB, 1.25 Mbit/s
[PASS] 15-31-00Z.mp4:   59.9 s, 899 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 15-32-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s
[PASS] 15-33-00Z.mp4:   12.3 s, 184 frames, 15.00 fps, 2.0 MB, 1.27 Mbit/s

Continuity (a new segment should start where the previous one ended):
  [WARN] 15-28-00Z.mp4 -> 15-31-00Z.mp4: +142.4 s unaccounted for
  [PASS] 15-31-00Z.mp4 -> 15-32-00Z.mp4: +0.1 s
  [PASS] 15-32-00Z.mp4 -> 15-33-00Z.mp4: +0.0 s

FAIL=0 WARN=1 stopped-and-restarted=0
RESULT: SEGMENTS OK

Test C:
ysak@ysak:~/surveillance $ python3 tools/set_setting.py storage.min_free_gb 56
storage.min_free_gb: 100.0 -> 56  (restart the recorder to apply)
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 23:40:41 INFO Main: Surveillance recorder 0.5.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 23:40:41 INFO Clock: System clock is NTP-synchronised
2026-09-23 23:40:41 INFO StorageManager: Deleted 2026-09-23/15-35-45Z.mp4 (2.3 MB): free space below 56 GB
2026-09-23 23:40:41 INFO StorageManager: Deleted 2026-09-23/15-36-00Z.mp4 (9.4 MB): free space below 56 GB
2026-09-23 23:40:41 INFO StorageManager: Deleted 2026-09-23/15-37-00Z.mp4 (9.4 MB): free space below 56 GB
2026-09-23 23:40:41 INFO StorageManager: Deleted 2026-09-23/15-38-00Z.mp4 (8.8 MB): free space below 56 GB
2026-09-23 23:40:41 WARNING StorageManager: Maximum storage 0.0 GB does not fit on this disk while keeping 56.0 GB free (room for about 0.0 GB); the free-space limit will apply first
2026-09-23 23:40:41 WARNING StorageManager: Storage low: only 50.23 GB free (minimum 56 GB); something other than recordings is filling the disk
2026-09-23 23:40:41 INFO StorageManager: Storage usage 0.00 GB / 0.03 GB, 50.2 GB free, 0 segments, oldest none
2026-09-23 23:40:42 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 23:40:42 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
^C2026-09-23 23:40:49 INFO Main: Received SIGINT, stopping
2026-09-23 23:40:49 INFO Recorder: Segment completed: 2026-09-23/15-40-42Z.mp4 start=2026-09-23T15:40:42.718+00:00 duration=6.8s frames=102 size=1.1MB
2026-09-23 23:40:49 INFO StorageManager: Deleted 2026-09-23/15-40-42Z.mp4 (1.1 MB): free space below 56 GB
2026-09-23 23:40:50 INFO Recorder: Stopped recording
2026-09-23 23:40:50 INFO Main: Surveillance recorder stopped
ysak@ysak:~/surveillance $ python3 tools/set_setting.py storage.min_free_gb 100
storage.min_free_gb: 56.0 -> 100  (restart the recorder to apply)
ysak@ysak:~/surveillance $ python3 -m app.main
2026-09-23 23:41:11 INFO Main: Surveillance recorder 0.5.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 23:41:11 INFO Clock: System clock is NTP-synchronised
2026-09-23 23:41:11 WARNING StorageManager: Maximum storage 0.0 GB does not fit on this disk while keeping 100.0 GB free (room for about 0.0 GB); the free-space limit will apply first
2026-09-23 23:41:11 WARNING StorageManager: Storage low: only 50.23 GB free (minimum 100 GB); something other than recordings is filling the disk
2026-09-23 23:41:11 INFO StorageManager: Storage usage 0.00 GB / 0.03 GB, 50.2 GB free, 0 segments, oldest none
2026-09-23 23:41:11 INFO Camera: Opened ov5647: main 1296x972, lores 640x480, 15 fps, sensor mode 1296x972
2026-09-23 23:41:11 INFO Recorder: Started recording to /home/ysak/surveillance/recordings (60 s segments, 2.50 Mbit/s, keyframe every 2 s)
^C2026-09-23 23:41:27 INFO Main: Received SIGINT, stopping
2026-09-23 23:41:27 INFO Recorder: Segment completed: 2026-09-23/15-41-12Z.mp4 start=2026-09-23T15:41:12.262+00:00 duration=15.6s frames=234 size=2.4MB
2026-09-23 23:41:28 INFO StorageManager: Deleted 2026-09-23/15-41-12Z.mp4 (2.4 MB): free space below 100 GB
2026-09-23 23:41:28 INFO Recorder: Stopped recording
2026-09-23 23:41:28 INFO Main: Surveillance recorder stopped

Test D:
ysak@ysak:~/surveillance $ python3 tools/set_setting.py paths.recordings_dir /mnt/cctv/recordings
paths.recordings_dir: 'recordings' -> '/mnt/cctv/recordings'  (restart the recorder to apply)
ysak@ysak:~/surveillance $ python3 tools/set_setting.py storage.required_mount /mnt/cctv
storage.required_mount: '' -> '/mnt/cctv'  (restart the recorder to apply)
ysak@ysak:~/surveillance $ python3 -m app.main           # should refuse and retry every 5, 10, 20 s; press Ctrl+C
2026-09-23 23:42:50 INFO Main: Surveillance recorder 0.5.0 starting (config /home/ysak/surveillance/config/settings.json, log /home/ysak/surveillance/logs/surveillance.log)
2026-09-23 23:42:50 INFO Clock: System clock is NTP-synchronised
2026-09-23 23:42:50 ERROR StorageManager: Storage unavailable: /mnt/cctv is not mounted; refusing to record onto the SD card
2026-09-23 23:42:50 ERROR Main: Could not start recording: /mnt/cctv is not mounted; refusing to record onto the SD card. Retrying in 5 s
2026-09-23 23:42:55 ERROR Main: Could not start recording: /mnt/cctv is not mounted; refusing to record onto the SD card. Retrying in 10 s
^C2026-09-23 23:42:56 INFO Main: Received SIGINT, stopping
2026-09-23 23:42:56 INFO Main: Surveillance recorder stopped
ysak@ysak:~/surveillance $ ls /mnt/cctv
ls: cannot access '/mnt/cctv': No such file or directory
ysak@ysak:~/surveillance $ python3 tools/set_setting.py storage.required_mount '""'
storage.required_mount: '/mnt/cctv' -> ''  (restart the recorder to apply)
ysak@ysak:~/surveillance $ python3 tools/set_setting.py paths.recordings_dir recordings
paths.recordings_dir: '/mnt/cctv/recordings' -> 'recordings'  (restart the recorder to apply)

