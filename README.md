Step 1;
ysak@ysak:~ $ cd ~/surveillance
cat > update_to_0_13_2.py <<'PYEOF'
#!/usr/bin/env python3
"""Update the surveillance project from 0.13.1 to 0.13.2 (run from ~/surveillance)."""
import os, shutil, sys
from pathlib import Path

EDITS = [
    ('app/__init__.py',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.13.1"\n',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.13.2"\n'),
    ('app/main.py',
     '    def _run(self) -> None:\n        while not self._halt.wait(SETTINGS_CHECK_S):\n            signature = self._file_signature()\n            if signature is None or signature == self._signature:\n',
     '    def _run(self) -> None:\n        while not self._halt.wait(SETTINGS_CHECK_S):\n            time.tzset()  # picks up a time zone change (Settings page) for the log timestamps\n            signature = self._file_signature()\n            if signature is None or signature == self._signature:\n'),
    ('app/web_settings.py',
     '  and this web process restarts itself so it uses the new values too.\n* Paths, network address and log file details are deliberately not editable from the browser.\n"""\nfrom __future__ import annotations\n',
     '  and this web process restarts itself so it uses the new values too.\n* Paths, network address and log file details are deliberately not editable from the browser.\n* Phase 13b: the time zone, and Restart / Shut down (password needed every time), through the\n  fixed actions in app/system_control.py.\n"""\nfrom static/style.css deploy/install.sh deploy/polkit-surveillance.rulestings.js web/
updated app/__init__.py  (backup: app/__init__.py.bak)
updated app/main.py  (backup: app/main.py.bak)
updated app/web_settings.py  (backup: app/web_settings.py.bak)
updated web/templates/settings.html  (backup: web/templates/settings.html.bak)
updated web/static/settings.js  (backup: web/static/settings.js.bak)
updated web/static/style.css  (backup: web/static/style.css.bak)
updated deploy/install.sh  (backup: deploy/install.sh.bak)
created app/system_control.py
created deploy/polkit-surveillance.rules
created web/templates/power.html
Update to 0.13.2 complete.
0b5d5ba0e3f7e13208aa526914efac646843d9c1147560e601f30ed5e374addd  app/__init__.py
248130a6b9a1966d701199edef6b0a05719b98a570be20492f24d8cc625f58b3  app/main.py
5a4e8f848ead733d0b971991ba44c10cab413b82476f380c77918a60efb921bb  app/web_settings.py
bf0ab9aaceb972a936e619b295f0efabebdb223c18cc01b0cc6bb2ea910d8373  app/system_control.py
2f319fb505310a186b81df0ef8efd7a29742847341ad42fa9b47058aa9f6cf27  web/templates/settings.html
45390f00fd175dd96b3945525952e49c32b616d544300982597129c758b9d13f  web/templates/power.html
7927b422d269c472ee4c985452259279141c5f23833b6c1b40a28247b33dee82  web/static/settings.js
10bab73a5a51356050575eaae19df59964b129c8dc7690cd83a7e85d62e0cec9  web/static/style.css
d395288187f59d6faa76fff9ff5958e35f6ce87e19e9892a8d7596e364486514  deploy/install.sh
d237237ded8de53942bd8d3f26a717499f2ba625dadd0aec6920c35372a41f16  deploy/polkit-surveillance.rules

Step 2:
ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/install.sh
[sudo] password for ysak: 

== Checking for copies started by hand
none

== Stopping the services (if they are running)

== Service user cctv
uid=999(cctv) gid=984(cctv) groups=984(cctv),44(video)

== Checking Python packages (as cctv)
found: picamera2 0.3.37, libcamera, cv2 4.10.0, numpy 2.2.4, av 14.2.0, simplejpeg 1.8.1, flask 3.1.1, waitress 3.0.2, argon2 25.1.0

== Installing the program into /opt/surveillance
version 0.13.2

== Folders
/etc/surveillance  /var/lib/surveillance  /var/log/surveillance

== Settings and data
settings  : /etc/surveillance/settings.json already exists, kept as it is
recordings: moved 0 item(s) from /home/ysak/surveillance/recordings to /var/lib/surveillance/recordings
database  : moved 0 item(s) from /home/ysak/surveillance/database to /var/lib/surveillance/database

== systemd units
polkit rule installed (Restart / Shut down / time zone from the web page)

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

Step3:
ysak@ysak:~/surveillance $ sudo systemd-run --quiet --wait --pipe --uid=cctv --gid=cctv timedatectl set-timezone UTC
sudo systemd-run --quiet --wait --pipe --uid=cctv --gid=cctv busctl call org.freedesktop.login1 /org/freedesktop/login1 org.freedesktop.login1.Manager CanReboot 
timedatectl show -p Timezone --value
Failed to set time zone: Interactive authentication required.
s "challenge"
Asia/Kuala_Lumpur

Step 4:
That password is wrong
Yup it did reboot and the live cam is on
