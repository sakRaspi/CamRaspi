Step 1:
ysak@ysak:~/surveillance $ cd ~/surveillance
cat > update_to_0_13_1.py <<'PYEOF'
#!/usr/bin/env python3
"""Update the surveillance project from 0.13.0 to 0.13.1 (run from ~/surveillance)."""
import os, shutil, sys
from pathlib import Path

EDITS = [
    ('app/__init__.py',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.13.0"\n',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.13.1"\n'),
    ('app/system_info.py',
     '\nCACHE_S = 30.0\n# Same value as `vcgencmd get_throttled`, readable without access to the VideoCore device.\nTHROTTLED_SYSFS = ("/sys/devices/platform/soc/soc:firmware/get_throttled",)\n\nTHROTTLE_FLAGS = {\n',
     '\nCACHE_S = 30.0\n# `vcgencmd get_throttled` needs the VideoCore device, which the web service cannot open; the\n# small surveillance-health service (deploy/throttle-monitor.sh) writes its answer here instead.\nTHROTTLED_FILE = "throttled"\nTHROTTLED_MAX_AGE_S = 120\n\nTHROTTLE_FLAGS = {\n'),
    ('app/system_info.py',
     '    """Thread-safe; CPU usage is measured between successive calls."""\n\n    def __init__(self) -> None:\n        self._lock = threading.Lock()\n        self._last_cpu: tuple[int, int] | None = None\n',
     '    """Thread-safe; CPU usage is measured between successive calls."""\n\n    def __init__(self, runtime_dir: Path | None = None) -> None:\n        self._runtime_dir = runtime_dir\n        self._lock = threading.Lock()\n        self._ervice deploy/install.sh deploy/throttle-monitor.sh deploy/surveillance-health.s
updated app/__init__.py  (backup: app/__init__.py.bak)
updated app/system_info.py  (backup: app/system_info.py.bak)
updated app/web.py  (backup: app/web.py.bak)
updated tools/phase4_check_segments.py  (backup: tools/phase4_check_segments.py.bak)
updated deploy/install.sh  (backup: deploy/install.sh.bak)
created deploy/throttle-monitor.sh
created deploy/surveillance-health.service
Update to 0.13.1 complete.
fa797233c06063bb7c213a10c11459969bd2646de1216a95dae28772e868d822  app/__init__.py
7f57671fbd009c21ed58d2059cc103a2c7ed30846fe2ed6cfc67ff5240b54dc8  app/system_info.py
678d00f0589a0e1b06c4af2f9910ab3090faf0a4557f7cad2fe6f1fc4c559538  app/web.py
39c5cb3d4cb0da0abaec74843d79bfeb132f238ba77d4a4fcf979e14b9080201  tools/phase4_check_segments.py
11b65c00df7eac9debe4cc96a9a7fc106250aaf9d325072bb86e8e417a1ac998  deploy/install.sh
dff6099f008535278ba437bb8b6a998c24dc0e2393d5bf12d9b00bb8d496b5b8  deploy/throttle-monitor.sh
c1cc086ba4df05d8fe2b4b02cf9b22e47d1144ffc6fbc80e04b77591db39f867  deploy/surveillance-health.service

Step 2:
ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/install.sh

== Checking for copies started by hand
none

== Stopping the services (if they are running)

== Service user cctv
uid=999(cctv) gid=984(cctv) groups=984(cctv),44(video)

== Checking Python packages (as cctv)
found: picamera2 0.3.37, libcamera, cv2 4.10.0, numpy 2.2.4, av 14.2.0, simplejpeg 1.8.1, flask 3.1.1, waitress 3.0.2, argon2 25.1.0

== Installing the program into /opt/surveillance
version 0.13.1

== Folders
/etc/surveillance  /var/lib/surveillance  /var/log/surveillance

== Settings and data
settings  : /etc/surveillance/settings.json already exists, kept as it is
recordings: moved 0 item(s) from /home/ysak/surveillance/recordings to /var/lib/surveillance/recordings
database  : moved 0 item(s) from /home/ysak/surveillance/database to /var/lib/surveillance/database

== systemd units
Created symlink '/etc/systemd/system/multi-user.target.wants/surveillance-health.service' → '/etc/systemd/system/surveillance-health.service'.

== Status (after 15 s)
  surveillance-recorder.service    active, 0 restart(s)
  surveillance-web.service         active, 0 restart(s)
  surveillance-health.service      active, 0 restart(s)
  recorder: recording

Done. Both services start at boot and restart themselves if they stop.
  Status:        systemctl status surveillance-recorder surveillance-web surveillance-health
  Problems:      journalctl -u surveillance-recorder -u surveillance-web -f
  Full log:      sudo tail -f /var/log/surveillance/surveillance.log
  Tools:         sudo cctv-tool set_setting --show     (sudo cctv-tool lists them all)
  Deploy again:  sudo bash /home/ysak/surveillance/deploy/install.sh     (after updating files in /home/ysak/surveillance)

Step 3:
ysak@ysak:~/surveillance $ sudo cat /run/surveillance/throttled
systemd-analyze security surveillance-health.service --no-pager | tail -1
sudo cctv-tool phase4_check_segments --last 20

throttled=0x0
→ Overall exposure level for surveillance-health.service: 1.0 OK 🙂
Checking 20 segment(s) in /var/lib/surveillance/recordings/2026-09-24 (segment length 300 s, 10 fps)

[PASS] 12-20-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 12-25-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 12-30-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 12-35-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 12-40-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 12-45-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 12-50-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 12-55-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 13-00-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 13-05-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 13-10-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 13-15-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 13-20-00Z.mp4:  251.9 s, 2520 frames, 10.00 fps, 26.3 MB, 0.83 Mbit/s
[PASS] 13-25-30Z.mp4:  269.2 s, 2693 frames, 10.00 fps, 28.1 MB, 0.83 Mbit/s
[PASS] 13-30-00Z.recovered.mp4:   92.0 s, 920 frames, 10.00 fps, 9.6 MB, 0.83 Mbit/s
[PASS] 13-31-43Z.recovered.mp4:   40.0 s, 400 frames, 10.00 fps, 4.2 MB, 0.83 Mbit/s
[PASS] 13-33-30Z.mp4:   89.2 s, 892 frames, 10.00 fps, 9.3 MB, 0.84 Mbit/s
[PASS] 13-35-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 13-40-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 13-45-00Z.mp4:   25.1 s, 251 frames, 10.00 fps, 2.6 MB, 0.84 Mbit/s

Continuity (a new segment should start where the previous one ended):
  [PASS] 12-20-00Z.mp4 -> 12-25-00Z.mp4: -0.0 s
  [PASS] 12-25-00Z.mp4 -> 12-30-00Z.mp4: -0.0 s
  [PASS] 12-30-00Z.mp4 -> 12-35-00Z.mp4: +0.1 s
  [PASS] 12-35-00Z.mp4 -> 12-40-00Z.mp4: -0.0 s
  [PASS] 12-40-00Z.mp4 -> 12-45-00Z.mp4: -0.0 s
  [PASS] 12-45-00Z.mp4 -> 12-50-00Z.mp4: +0.1 s
  [PASS] 12-50-00Z.mp4 -> 12-55-00Z.mp4: -0.0 s
  [PASS] 12-55-00Z.mp4 -> 13-00-00Z.mp4: -0.0 s
  [PASS] 13-00-00Z.mp4 -> 13-05-00Z.mp4: +0.1 s
  [PASS] 13-05-00Z.mp4 -> 13-10-00Z.mp4: -0.0 s
  [PASS] 13-10-00Z.mp4 -> 13-15-00Z.mp4: +0.1 s
  [PASS] 13-15-00Z.mp4 -> 13-20-00Z.mp4: -0.0 s
  [INFO] 13-20-00Z.mp4 -> 13-25-30Z.mp4: recorder stopped/restarted, 0:01:18 not in completed segments
  [PASS] 13-25-30Z.mp4 -> 13-30-00Z.recovered.mp4: +0.8 s
  [INFO] 13-30-00Z.recovered.mp4 -> 13-31-43Z.recovered.mp4: recorder stopped/restarted, 0:00:11 not in completed segments
  [INFO] 13-31-43Z.recovered.mp4 -> 13-33-30Z.mp4: recorder stopped/restarted, 0:01:07 not in completed segments
  [PASS] 13-33-30Z.mp4 -> 13-35-00Z.mp4: +0.8 s
  [PASS] 13-35-00Z.mp4 -> 13-40-00Z.mp4: +0.1 s
  [PASS] 13-40-00Z.mp4 -> 13-45-00Z.mp4: -0.0 s

Unfinished .partial files (the newest one is normal while the recorder is running):
  13-45-39Z.mp4.partial: 8.7 MB, 82.8 s playable

FAIL=0 WARN=0 stopped-and-restarted=3
RESULT: SEGMENTS OK

Step 4:
Yup after the PI reboot, the cam is Live
ysak@ysak:~ $ systemctl is-active surveillance-recorder surveillance-web surveillance-health
journalctl -b -u surveillance-recorder -o short-monotonic --no-pager | grep -m1 "Started surveillance"
grep "Started recording" /var/log/surveillance/surveillance.log | tail -1
active
active
active
[   16.641588] ysak systemd[1]: Started surveillance-recorder.service - Surveillance camera recorder.
2026-09-24 21:48:49 INFO Recorder: Started recording to /var/lib/surveillance/recordings (300 s segments, 2.50 Mbit/s, keyframe every 2 s)

