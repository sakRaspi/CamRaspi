Ping on tailnet:
sak@ysak:~/surveillance $ ping -c 3 100.85.224.6
PING 100.85.224.6 (100.85.224.6) 56(84) bytes of data.

--- 100.85.224.6 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2033ms


Step 1:
ysak@ysak:~/surveillance $ pgrep -af "app.main|app.web_main" || echo "nothing running"
nothing running

Step 2:
ysak@ysak:~/surveillance $ cd ~/surveillance
cat > update_to_0_13_0.py <<'PYEOF'
#!/usr/bin/env python3
"""Update the surveillance project from 0.12.0 to 0.13.0 (run from ~/surveillance)."""
import os, shutil, sys
from pathlib import Path

EDITS = [
    ('app/__init__.py',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.12.0"\n',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.13.0"\n'),
    ('app/auth.py',
     '\nfrom argon2 import PasswordHasher\nfrom argon2.exceptions import InvalidHashError, VerificationError, VerifyMismatchError\n\nlog = logging.getLogger("Auth")\n',
     '\nfrom argon2 import PasswordHasher\nfrom argon2.exceptions import InvalidHash, VerificationError, VerifyMismatchError\n\nlog = logging.getLogger("Auth")\n'),
    ('app/auth.py',
     'AUDIT_KEPT = 5000\nLOCKOUT_FORGET_S = 86400\n\nCOMMON_PASSWORDS = {\n',
     'AUDIT_KEPT = 5000\nLOCKOUT_FORGET_S = 86400\n# Argon2id, RFC 9106 "low memory" profile (64 MiB, 3 passes, 4 lanes). Fixed here rather than\n# taken from the library defaults, which differ between versions (Debian 13 ships 21.1).\nARGON2_PARAMS = {"time_cost": 3, "memory_cost": 65536, "parallelism": 4, "hash_len":-web.service deploy/journald-surveillance.confcorder.service deploy/surveillance
updated app/__init__.py  (backup: app/__init__.py.bak)
updated app/auth.py  (backup: app/auth.py.bak)
updated app/config.py  (backup: app/config.py.bak)
updated app/logging_setup.py  (backup: app/logging_setup.py.bak)
updated app/main.py  (backup: app/main.py.bak)
updated app/system_info.py  (backup: app/system_info.py.bak)
created app/systemd_notify.py
created deploy/install.sh
created deploy/setup_config.py
created deploy/cctv-tool
created deploy/surveillance-recorder.service
created deploy/surveillance-web.service
created deploy/journald-surveillance.conf
Update to 0.13.0 complete.
5b101b8aca647f053a311dec1774618e77db16cbd55fd16cb90ac392eb9ad08e  app/__init__.py
8346f70bcd7e38c11dfcf82684a3118cda44a81c2adfbd072cf6496a0f7b5fb0  app/auth.py
005317889102077591a8c91b3b48af8169d7f36cca0a5c334b57e164176f4ebe  app/config.py
ad081540ad455f1a5e93a5983cff16a48222f37f419278fc59c08d30c389ab52  app/logging_setup.py
77699e69429d8855c45a9de72071f002aa26dda48de163cdfbaafb70b249011a  app/main.py
71393c63c87d15402d1f3d9becff543ea55b01ab5d73b2c0a09176141662564a  app/system_info.py
8abbbe56d8bba9363571dc537c4d1e9c1c98f7654e5b39609bbd95d540400fe5  app/systemd_notify.py
ac1d2f62578acfe7787fde809cb307bfa4553ef2b9fe407e9c54daa95afa2af2  deploy/install.sh
9772bf8523acbcf750bbe284e22ed96f38d2e9cb9e6cd4b71a23e638a3b452c0  deploy/setup_config.py
bf33ea14d2a89536b1b1e8a71a7b797717d362f6ac9e32800a52471a8a3f6632  deploy/cctv-tool
ec7bd0bc15390600fbdfb60378c65dd1aec7c3b94785ea09f9fa79f130d97e88  deploy/surveillance-recorder.service
3be52e3915f385cec45d7f8195af9b82b3344d940bc190f1392241d0b8d684d9  deploy/surveillance-web.service
6116b276eabf5b77905c5309376ee9e9ac30459250cad8fd2ebd88574c3a4f1c  deploy/journald-surveillance.conf


Step 3:
ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/install.sh
[sudo] password for ysak: 

== Checking for copies started by hand
none

== Stopping the services (if they are running)

== Service user cctv
created
ysak added to group cctv (read access to recordings and logs; log in again to use it)
uid=999(cctv) gid=984(cctv) groups=984(cctv),44(video)

== Checking Python packages (as cctv)
found: picamera2 0.3.37, libcamera, cv2 4.10.0, numpy 2.2.4, av 14.2.0, simplejpeg 1.8.1, flask 3.1.1, waitress 3.0.2, argon2 25.1.0

== Installing the program into /opt/surveillance
version 0.13.0

== Folders
/etc/surveillance  /var/lib/surveillance  /var/log/surveillance

== Settings and data
settings  : created /etc/surveillance/settings.json from /home/ysak/surveillance/config/settings.json
recordings: moved 2 item(s) from /home/ysak/surveillance/recordings to /var/lib/surveillance/recordings
database  : moved 11 item(s) from /home/ysak/surveillance/database to /var/lib/surveillance/database

== systemd units
Created symlink '/etc/systemd/system/multi-user.target.wants/surveillance-recorder.service' → '/etc/systemd/system/surveillance-recorder.service'.
Created symlink '/etc/systemd/system/multi-user.target.wants/surveillance-web.service' → '/etc/systemd/system/surveillance-web.service'.

== Status (after 15 s)
  surveillance-recorder.service    active, 0 restart(s)
  surveillance-web.service         active, 0 restart(s)
  recorder: recording

Done. Both services start at boot and restart themselves if they stop.
  Status:        systemctl status surveillance-recorder surveillance-web
  Problems:      journalctl -u surveillance-recorder -u surveillance-web -f
  Full log:      sudo tail -f /var/log/surveillance/surveillance.log
  Tools:         sudo cctv-tool set_setting --show     (sudo cctv-tool lists them all)
  Deploy again:  sudo bash /home/ysak/surveillance/deploy/install.sh     (after updating files in /home/ysak/surveillance)

Step 4:
ysak@ysak:~/surveillance $ systemctl status surveillance-recorder surveillance-web --no-pager
● surveillance-recorder.service - Surveillance camera recorder
     Loaded: loaded (/etc/systemd/system/surveillance-recorder.service; enabled; preset: enabled)
     Active: active (running) since Thu 2026-09-24 21:25:27 +08; 1min 29s ago
 Invocation: 818a821f1afe4b06aa1f4d9e5e3f2faa
   Main PID: 15885 (python3)
     Status: "recording"
      Tasks: 31 (limit: 3960)
        CPU: 16.466s
     CGroup: /system.slice/surveillance-recorder.service
             └─15885 /usr/bin/python3 -m app.main

Sep 24 21:25:26 ysak systemd[1]: Starting surveillance-recorder.service - …er...
Sep 24 21:25:27 ysak systemd[1]: Started surveillance-recorder.service - S…rder.

● surveillance-web.service - Surveillance camera web interface
     Loaded: loaded (/etc/systemd/system/surveillance-web.service; enabled; preset: enabled)
     Active: active (running) since Thu 2026-09-24 21:25:27 +08; 1min 29s ago
 Invocation: 086deaf49c2a491890af45628e00b357
   Main PID: 15899 (python3)
      Tasks: 8 (limit: 3960)
        CPU: 1.877s
     CGroup: /system.slice/surveillance-web.service
             └─15899 /usr/bin/python3 -m app.web_main

Sep 24 21:25:27 ysak systemd[1]: Started surveillance-web.service - Survei…face.
Hint: Some lines were ellipsized, use -l to show in full.

Step 5:
ysak@ysak:~/surveillance $ sudo cctv-tool phase4_check_segments
sudo cctv-tool manage_users list
sudo cctv-tool phase12_tailscale_check
Checking 94 segment(s) in /var/lib/surveillance/recordings/2026-09-24 (segment length 300 s, 10 fps)

[FAIL] 06-03-58Z.mp4:    1.5 s, 22 frames, 15.00 fps, 0.2 MB, 1.19 Mbit/s  <- 15.00 fps
[FAIL] 06-04-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-05-00Z.mp4:   60.1 s, 901 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-06-00Z.mp4:    2.0 s, 29 frames, 15.00 fps, 0.3 MB, 1.23 Mbit/s  <- 15.00 fps
[FAIL] 06-07-45Z.recovered.mp4:   10.0 s, 150 frames, 15.00 fps, 1.5 MB, 1.23 Mbit/s  <- 15.00 fps
[FAIL] 06-08-09Z.mp4:   50.5 s, 757 frames, 15.00 fps, 7.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-09-00Z.mp4:   10.0 s, 150 frames, 15.00 fps, 1.5 MB, 1.23 Mbit/s  <- 15.00 fps
[FAIL] 06-10-23Z.mp4:    7.6 s, 113 frames, 15.00 fps, 1.2 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-11-46Z.mp4:   13.2 s, 197 frames, 15.00 fps, 2.1 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-12-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-13-00Z.mp4:   39.7 s, 595 frames, 15.00 fps, 6.2 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-37-44Z.mp4:  135.5 s, 2033 frames, 15.00 fps, 21.2 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-40-00Z.mp4:  300.0 s, 4496 frames, 14.99 fps, 46.9 MB, 1.25 Mbit/s  <- 1 timestamp gap(s) at 160.4 s (+400 ms), 14.99 fps
[FAIL] 06-45-00Z.mp4:   63.9 s, 958 frames, 15.00 fps, 10.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-46-10Z.mp4:  230.0 s, 3450 frames, 15.00 fps, 36.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-50-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 06-55-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-00-00Z.mp4:  205.7 s, 3086 frames, 15.00 fps, 32.2 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-10-53Z.mp4:  246.6 s, 3699 frames, 15.00 fps, 38.6 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-15-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 47.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-20-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-25-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 47.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-30-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-35-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 47.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-40-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-45-00Z.mp4:    9.6 s, 144 frames, 15.00 fps, 1.5 MB, 1.22 Mbit/s  <- 15.00 fps
[FAIL] 07-45-41Z.mp4:  258.4 s, 3876 frames, 15.00 fps, 40.5 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-50-00Z.mp4:  300.0 s, 4500 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 07-55-00Z.mp4:  300.1 s, 4502 frames, 15.00 fps, 47.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-00-00Z.mp4:  300.0 s, 4500 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-05-00Z.mp4:    1.7 s, 25 frames, 15.00 fps, 0.3 MB, 1.28 Mbit/s  <- 15.00 fps
[FAIL] 08-05-09Z.mp4:   38.6 s, 579 frames, 15.00 fps, 6.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-06-16Z.recovered.mp4:  162.0 s, 2430 frames, 15.00 fps, 25.3 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-09-30Z.mp4:   29.8 s, 447 frames, 15.00 fps, 4.7 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-10-00Z.mp4:  300.0 s, 4500 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-15-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-20-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-25-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 47.0 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-30-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-35-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-40-00Z.mp4:  300.0 s, 4501 frames, 15.00 fps, 46.9 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 08-45-00Z.mp4:   65.8 s, 986 frames, 15.00 fps, 10.2 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 09-18-33Z.mp4:   42.4 s, 635 frames, 15.00 fps, 6.7 MB, 1.26 Mbit/s  <- 15.00 fps
[FAIL] 09-19-23Z.mp4:   36.8 s, 551 frames, 15.00 fps, 5.8 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 09-20-00Z.mp4:  193.8 s, 2906 frames, 15.00 fps, 30.3 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 09-23-27Z.mp4:   32.1 s, 481 frames, 15.00 fps, 5.1 MB, 1.26 Mbit/s  <- 15.00 fps
[FAIL] 09-24-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.3 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 09-25-00Z.mp4:   60.0 s, 900 frames, 15.00 fps, 9.4 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 09-26-00Z.mp4:   24.2 s, 362 frames, 15.00 fps, 3.8 MB, 1.27 Mbit/s  <- 15.00 fps
[FAIL] 09-27-30Z.mp4:  149.5 s, 2243 frames, 15.00 fps, 23.4 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 09-30-00Z.mp4:  190.1 s, 2850 frames, 15.00 fps, 29.7 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 10-09-54Z.mp4:    5.8 s, 87 frames, 15.00 fps, 0.9 MB, 1.26 Mbit/s  <- 15.00 fps
[FAIL] 10-10-00Z.mp4:  126.5 s, 1898 frames, 15.00 fps, 19.8 MB, 1.25 Mbit/s  <- 15.00 fps
[FAIL] 10-12-08Z.mp4:   34.2 s, 512 frames, 14.97 fps, 5.4 MB, 1.25 Mbit/s  <- 14.97 fps
[FAIL] 10-15-58Z.mp4:   22.0 s, 329 frames, 14.96 fps, 3.4 MB, 1.25 Mbit/s  <- 14.96 fps
[FAIL] 10-16-22Z.mp4:    4.2 s, 63 frames, 15.00 fps, 0.7 MB, 1.33 Mbit/s  <- 15.00 fps
[FAIL] 10-18-05Z.mp4:   42.1 s, 630 frames, 14.98 fps, 6.6 MB, 1.25 Mbit/s  <- 14.98 fps
[PASS] 10-18-49Z.mp4:   70.6 s, 706 frames, 10.00 fps, 7.4 MB, 0.84 Mbit/s
[PASS] 10-20-00Z.mp4:   58.3 s, 583 frames, 10.00 fps, 6.1 MB, 0.84 Mbit/s
[PASS] 10-20-59Z.mp4:  240.1 s, 2402 frames, 10.00 fps, 25.1 MB, 0.84 Mbit/s
[PASS] 10-25-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 10-30-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 10-35-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 10-40-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 10-45-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 10-50-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 10-55-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 11-00-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 11-05-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 11-10-00Z.mp4:  300.0 s, 3001 frames, 10.00 fps, 31.3 MB, 0.84 Mbit/s
[PASS] 11-15-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
[PASS] 11-20-00Z.mp4:   53.1 s, 531 frames, 10.00 fps, 5.5 MB, 0.84 Mbit/s
[PASS] 11-21-54Z.mp4:   47.0 s, 470 frames, 10.00 fps, 4.9 MB, 0.84 Mbit/s
[PASS] 11-22-42Z.mp4:   18.9 s, 189 frames, 10.00 fps, 2.0 MB, 0.84 Mbit/s
[PASS] 11-25-42Z.mp4:   57.0 s, 570 frames, 10.00 fps, 6.0 MB, 0.84 Mbit/s
[PASS] 11-26-41Z.mp4:   13.9 s, 139 frames, 10.00 fps, 1.5 MB, 0.84 Mbit/s
[PASS] 11-26-57Z.mp4:   18.9 s, 189 frames, 10.00 fps, 2.0 MB, 0.84 Mbit/s
[PASS] 11-27-18Z.mp4:  161.9 s, 1619 frames, 10.00 fps, 16.9 MB, 0.83 Mbit/s
[PASS] 11-30-00Z.mp4:   26.5 s, 265 frames, 10.00 fps, 2.8 MB, 0.84 Mbit/s
[PASS] 12-13-53Z.mp4:   66.3 s, 663 frames, 10.00 fps, 6.9 MB, 0.84 Mbit/s
[PASS] 12-15-00Z.mp4:  299.9 s, 3000 frames, 10.00 fps, 31.3 MB, 0.83 Mbit/s
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

Continuity (a new segment should start where the previous one ended):
  [INFO] 06-03-58Z.mp4 -> 06-04-00Z.mp4: recorder stopped/restarted, 0:00:01 not in completed segments
  [PASS] 06-04-00Z.mp4 -> 06-05-00Z.mp4: -0.0 s
  [INFO] 06-05-00Z.mp4 -> 06-06-00Z.mp4: recorder stopped/restarted, 0:00:00 not in completed segments
  [INFO] 06-06-00Z.mp4 -> 06-07-45Z.recovered.mp4: recorder stopped/restarted, 0:01:43 not in completed segments
  [INFO] 06-07-45Z.recovered.mp4 -> 06-08-09Z.mp4: recorder stopped/restarted, 0:00:14 not in completed segments
  [INFO] 06-08-09Z.mp4 -> 06-09-00Z.mp4: recorder stopped/restarted, 0:00:01 not in completed segments
  [INFO] 06-09-00Z.mp4 -> 06-10-23Z.mp4: recorder stopped/restarted, 0:01:13 not in completed segments
  [INFO] 06-10-23Z.mp4 -> 06-11-46Z.mp4: recorder stopped/restarted, 0:01:15 not in completed segments
  [INFO] 06-11-46Z.mp4 -> 06-12-00Z.mp4: recorder stopped/restarted, 0:00:01 not in completed segments
  [INFO] 06-12-00Z.mp4 -> 06-13-00Z.mp4: recorder stopped/restarted, 0:00:00 not in completed segments
  [INFO] 06-13-00Z.mp4 -> 06-37-44Z.mp4: recorder stopped/restarted, 0:24:04 not in completed segments
  [PASS] 06-37-44Z.mp4 -> 06-40-00Z.mp4: +0.5 s
  [PASS] 06-40-00Z.mp4 -> 06-45-00Z.mp4: -0.0 s
  [INFO] 06-45-00Z.mp4 -> 06-46-10Z.mp4: recorder stopped/restarted, 0:00:06 not in completed segments
  [PASS] 06-46-10Z.mp4 -> 06-50-00Z.mp4: +0.0 s
  [PASS] 06-50-00Z.mp4 -> 06-55-00Z.mp4: -0.0 s
  [PASS] 06-55-00Z.mp4 -> 07-00-00Z.mp4: -0.0 s
  [INFO] 07-00-00Z.mp4 -> 07-10-53Z.mp4: recorder stopped/restarted, 0:07:27 not in completed segments
  [PASS] 07-10-53Z.mp4 -> 07-15-00Z.mp4: +0.4 s
  [PASS] 07-15-00Z.mp4 -> 07-20-00Z.mp4: -0.0 s
  [PASS] 07-20-00Z.mp4 -> 07-25-00Z.mp4: -0.0 s
  [PASS] 07-25-00Z.mp4 -> 07-30-00Z.mp4: -0.0 s
  [PASS] 07-30-00Z.mp4 -> 07-35-00Z.mp4: -0.0 s
  [PASS] 07-35-00Z.mp4 -> 07-40-00Z.mp4: -0.0 s
  [PASS] 07-40-00Z.mp4 -> 07-45-00Z.mp4: -0.0 s
  [INFO] 07-45-00Z.mp4 -> 07-45-41Z.mp4: recorder stopped/restarted, 0:00:31 not in completed segments
  [PASS] 07-45-41Z.mp4 -> 07-50-00Z.mp4: +0.6 s
  [PASS] 07-50-00Z.mp4 -> 07-55-00Z.mp4: +0.0 s
  [PASS] 07-55-00Z.mp4 -> 08-00-00Z.mp4: -0.1 s
  [PASS] 08-00-00Z.mp4 -> 08-05-00Z.mp4: +0.0 s
  [INFO] 08-05-00Z.mp4 -> 08-05-09Z.mp4: recorder stopped/restarted, 0:00:07 not in completed segments
  [INFO] 08-05-09Z.mp4 -> 08-06-16Z.recovered.mp4: recorder stopped/restarted, 0:00:28 not in completed segments
  [INFO] 08-06-16Z.recovered.mp4 -> 08-09-30Z.mp4: recorder stopped/restarted, 0:00:32 not in completed segments
  [PASS] 08-09-30Z.mp4 -> 08-10-00Z.mp4: +0.2 s
  [PASS] 08-10-00Z.mp4 -> 08-15-00Z.mp4: +0.0 s
  [PASS] 08-15-00Z.mp4 -> 08-20-00Z.mp4: -0.0 s
  [PASS] 08-20-00Z.mp4 -> 08-25-00Z.mp4: -0.0 s
  [PASS] 08-25-00Z.mp4 -> 08-30-00Z.mp4: -0.0 s
  [PASS] 08-30-00Z.mp4 -> 08-35-00Z.mp4: -0.0 s
  [PASS] 08-35-00Z.mp4 -> 08-40-00Z.mp4: -0.0 s
  [PASS] 08-40-00Z.mp4 -> 08-45-00Z.mp4: -0.0 s
  [INFO] 08-45-00Z.mp4 -> 09-18-33Z.mp4: recorder stopped/restarted, 0:32:27 not in completed segments
  [INFO] 09-18-33Z.mp4 -> 09-19-23Z.mp4: recorder stopped/restarted, 0:00:08 not in completed segments
  [PASS] 09-19-23Z.mp4 -> 09-20-00Z.mp4: +0.2 s
  [INFO] 09-20-00Z.mp4 -> 09-23-27Z.mp4: recorder stopped/restarted, 0:00:13 not in completed segments
  [INFO] 09-23-27Z.mp4 -> 09-24-00Z.mp4: recorder stopped/restarted, 0:00:01 not in completed segments
  [PASS] 09-24-00Z.mp4 -> 09-25-00Z.mp4: -0.0 s
  [INFO] 09-25-00Z.mp4 -> 09-26-00Z.mp4: recorder stopped/restarted, 0:00:00 not in completed segments
  [INFO] 09-26-00Z.mp4 -> 09-27-30Z.mp4: recorder stopped/restarted, 0:01:06 not in completed segments
  [PASS] 09-27-30Z.mp4 -> 09-30-00Z.mp4: +0.5 s
  [INFO] 09-30-00Z.mp4 -> 10-09-54Z.mp4: recorder stopped/restarted, 0:36:44 not in completed segments
  [PASS] 10-09-54Z.mp4 -> 10-10-00Z.mp4: +0.2 s
  [INFO] 10-10-00Z.mp4 -> 10-12-08Z.mp4: recorder stopped/restarted, 0:00:01 not in completed segments
  [INFO] 10-12-08Z.mp4 -> 10-15-58Z.mp4: recorder stopped/restarted, 0:03:16 not in completed segments
  [INFO] 10-15-58Z.mp4 -> 10-16-22Z.mp4: recorder stopped/restarted, 0:00:02 not in completed segments
  [INFO] 10-16-22Z.mp4 -> 10-18-05Z.mp4: recorder stopped/restarted, 0:01:39 not in completed segments
  [INFO] 10-18-05Z.mp4 -> 10-18-49Z.mp4: recorder stopped/restarted, 0:00:02 not in completed segments
  [PASS] 10-18-49Z.mp4 -> 10-20-00Z.mp4: +0.4 s
  [INFO] 10-20-00Z.mp4 -> 10-20-59Z.mp4: recorder stopped/restarted, 0:00:01 not in completed segments
  [PASS] 10-20-59Z.mp4 -> 10-25-00Z.mp4: +0.9 s
  [PASS] 10-25-00Z.mp4 -> 10-30-00Z.mp4: +0.1 s
  [PASS] 10-30-00Z.mp4 -> 10-35-00Z.mp4: -0.0 s
  [PASS] 10-35-00Z.mp4 -> 10-40-00Z.mp4: +0.1 s
  [PASS] 10-40-00Z.mp4 -> 10-45-00Z.mp4: -0.0 s
  [PASS] 10-45-00Z.mp4 -> 10-50-00Z.mp4: +0.1 s
  [PASS] 10-50-00Z.mp4 -> 10-55-00Z.mp4: -0.0 s
  [PASS] 10-55-00Z.mp4 -> 11-00-00Z.mp4: -0.0 s
  [PASS] 11-00-00Z.mp4 -> 11-05-00Z.mp4: +0.1 s
  [PASS] 11-05-00Z.mp4 -> 11-10-00Z.mp4: -0.0 s
  [PASS] 11-10-00Z.mp4 -> 11-15-00Z.mp4: -0.0 s
  [PASS] 11-15-00Z.mp4 -> 11-20-00Z.mp4: +0.1 s
  [INFO] 11-20-00Z.mp4 -> 11-21-54Z.mp4: recorder stopped/restarted, 0:01:01 not in completed segments
  [INFO] 11-21-54Z.mp4 -> 11-22-42Z.mp4: recorder stopped/restarted, 0:00:01 not in completed segments
  [INFO] 11-22-42Z.mp4 -> 11-25-42Z.mp4: recorder stopped/restarted, 0:02:41 not in completed segments
  [INFO] 11-25-42Z.mp4 -> 11-26-41Z.mp4: recorder stopped/restarted, 0:00:02 not in completed segments
  [INFO] 11-26-41Z.mp4 -> 11-26-57Z.mp4: recorder stopped/restarted, 0:00:02 not in completed segments
  [INFO] 11-26-57Z.mp4 -> 11-27-18Z.mp4: recorder stopped/restarted, 0:00:02 not in completed segments
  [PASS] 11-27-18Z.mp4 -> 11-30-00Z.mp4: +0.1 s
  [INFO] 11-30-00Z.mp4 -> 12-13-53Z.mp4: recorder stopped/restarted, 0:43:27 not in completed segments
  [PASS] 12-13-53Z.mp4 -> 12-15-00Z.mp4: +0.7 s
  [PASS] 12-15-00Z.mp4 -> 12-20-00Z.mp4: +0.1 s
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

Unfinished .partial files (the newest one is normal while the recorder is running):
Traceback (most recent call last):
  File "/opt/surveillance/tools/phase4_check_segments.py", line 157, in <module>
    sys.exit(main())
             ~~~~^^
  File "/opt/surveillance/tools/phase4_check_segments.py", line 146, in main
    print(f"  {path.name}: {path.stat().st_size / 1e6:.1f} MB, {playable:.1f} s playable")
                            ~~~~~~~~~^^
  File "/usr/lib/python3.13/pathlib/_local.py", line 517, in stat
    return os.stat(self, follow_symlinks=follow_symlinks)
           ~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
FileNotFoundError: [Errno 2] No such file or directory: '/var/lib/surveillance/recordings/2026-09-24/13-25-30Z.mp4.partial'
admin                created 2026-09-24 17:50  password changed 2026-09-24 17:50  sessions 7  failed logins 0
Tailscale
  [PASS] Tailscale state: Running (version 1.102.4)
         this Pi in the tailnet: cam01.tail1c1671.ts.net  100.91.247.39 fd7a:115c:a1e0::5536:f728
  [PASS] MagicDNS on
  [PASS] device tags: tag:camera
  [PASS] device key does not expire
  [PASS] HTTPS certificates enabled
tailscale serve
  [PASS] https://cam01.tail1c1671.ts.net -> http://127.0.0.1:8080
  [PASS] Funnel is off: nothing is shared outside the tailnet
Web interface
  [PASS] web.https_hostname = cam01.tail1c1671.ts.net
Listening ports
  [PASS] web interface port 8080 listens on this Pi only (127.0.0.1)
  [WARN] reachable from the home network: 0.0.0.0:22: SSH, restricted to the tailnet in Phase 14
         tailnet only : 100.91.247.39:443
         tailnet only : 100.91.247.39:55890
  [WARN] reachable from the home network: :::22: SSH, restricted to the tailnet in Phase 14
         tailnet only : fd7a:115c:a1e0::5536:f728:443
         tailnet only : fd7a:115c:a1e0::5536:f728:40880
End-to-end HTTPS through the tailnet
  [PASS] HTTPS certificate valid for cam01.tail1c1671.ts.net (issued by Let's Encrypt, renews automatically, 89 days left)
  [PASS] https://cam01.tail1c1671.ts.net/login answers 200 through tailscale serve
  [PASS] browsers are told to always use HTTPS for this name (HSTS)
  [PASS] https://cam01.tail1c1671.ts.net/api/status without logging in answers 401 (expected 401)

RESULT: TAILNET ACCESS OK  (13 pass, 2 warn, 0 fail)

Step 6:
ysak@ysak:~/surveillance $ sudo systemctl kill -s KILL surveillance-recorder; sleep 10
ysak@ysak:~/surveillance $ systemctl show -p NRestarts -p StatusText surveillance-recorder
StatusText=recording
NRestarts=1

ysak@ysak:~/surveillance $ sudo systemctl kill -s STOP surveillance-recorder; sleep 75
journalctl -u surveillance-recorder --since "-3min" --no-pager | grep -E "Watchdog timeout|Started"
systemctl show -p NRestarts -p StatusText surveillance-recorder
ysak@ysak:~/surveillance $ journalctl -u surveillance-recorder --since "-3min" --no-pager | grep -E "Watchdog timeout|Started"
Sep 24 21:31:40 ysak systemd[1]: Started surveillance-recorder.service - Surveillance camera recorder.
Sep 24 21:33:20 ysak systemd[1]: surveillance-recorder.service: Watchdog timeout (limit 1min)!
Sep 24 21:33:28 ysak systemd[1]: Started surveillance-recorder.service - Surveillance camera recorder.
ysak@ysak:~/surveillance $ systemctl show -p NRestarts -p StatusText surveillance-recorder
StatusText=recording
NRestarts=2

ysak@ysak:~/surveillance $ journalctl -b -u surveillance-recorder -o short-monotonic --no-pager | grep -m1 "Started surveillance"
sudo grep -m1 "Started recording" /var/log/surveillance/surveillance.log
[26822.662334] ysak systemd[1]: Started surveillance-recorder.service - Surveillance camera recorder.
2026-09-24 21:25:29 INFO Recorder: Started recording to /var/lib/surveillance/recordings (300 s segments, 2.50 Mbit/s, keyframe every 2 s)

Step 7:
ysak@ysak:~/surveillance $ systemd-analyze security surveillance-recorder.service surveillance-web.service --no-pager | tail -3
sudo systemd-run --quiet --wait --pipe -p IPAddressAllow=localhost -p IPAddressDeny=any curl -m 5 -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8080/login
sudo systemd-run --quiet --wait --pipe -p IPAddressAllow=localhost -p IPAddressDeny=any curl -m 5 -s -o /dev/null -w "%{http_code}\n" https://1.1.1.1
cat /sys/devices/platform/soc/soc:firmware/get_throttled
UNIT                          EXPOSURE PREDICATE HAPPY
surveillance-recorder.service      1.3 OK        🙂
surveillance-web.service           1.4 OK        🙂
200
000
cat: '/sys/devices/platform/soc/soc:firmware/get_throttled': No such file or directory

Step 8:
ysak@ysak:~/surveillance $ top -b -n 2 -d 5 -p "$(pgrep -d, -f 'app.main|app.web_main')"
top - 21:35:25 up  7:37,  7 users,  load average: 1.35, 0.80, 0.59
Tasks:   2 total,   0 running,   2 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.4 us,  0.0 sy,  0.0 ni, 97.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st 
MiB Mem :   3789.8 total,     97.0 free,   1414.4 used,   2482.6 buff/cache     
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2375.3 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  16508 cctv      15  -5 2502204 191968 127816 S  20.0   4.9   0:19.15 python3
  15899 cctv      20   0  599240  43084  13632 S   0.0   1.1   0:05.76 python3

top - 21:35:30 up  7:37,  7 users,  load average: 1.24, 0.79, 0.59
Tasks:   2 total,   0 running,   2 sleeping,   0 stopped,   0 zombie
%Cpu(s):  4.5 us,  3.6 sy,  0.0 ni, 91.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st 
MiB Mem :   3789.8 total,     93.3 free,   1417.6 used,   2487.1 buff/cache     
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2372.1 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  16508 cctv      15  -5 2502204 191988 127816 S  12.8   4.9   0:19.79 python3
  15899 cctv      20   0  599240  43540  13632 S   0.4   1.1   0:05.78 python3

