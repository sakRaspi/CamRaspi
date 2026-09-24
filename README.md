SSH:
ysak@ysak:~/surveillance $ ls -l --time-style=+%H:%M /etc/ssh/sshd_config.d/ /etc/ssh/sshd_config
grep -n "^Include" /etc/ssh/sshd_config
sudo sshd -T | grep -E "^(passwordauthentication|permitrootlogin|allowusers) "
-rw-r--r-- 1 root root 3424 20:33 /etc/ssh/sshd_config

/etc/ssh/sshd_config.d/:
total 4
-rw------- 1 root root 27 08:18 50-cloud-init.conf
12:Include /etc/ssh/sshd_config.d/*.conf
[sudo] password for ysak: 
Sorry, try again.
[sudo] password for ysak: 
permitrootlogin without-password
passwordauthentication yes

Connection Timeout

Checksum:
ysak@ysak:~/surveillance $ cd ~/surveillance
cat > update_to_0_15_0.py <<'PYEOF'
#!/usr/bin/env python3
"""Update the surveillance project from 0.14.0 to 0.15.0 (run from ~/surveillance)."""
import os, shutil, sys
from pathlib import Path

EDITS = [
    ('app/__init__.py',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.14.0"\n',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.15.0"\n'),
    ('app/auth.py',
     'MAX_PASSWORD_LENGTH = 256\nUSERNAME_RE = re.compile(r"^[a-z][a-z0-9_.-]{2,31}$")\nTOKEN_BYTES = 32\nMAX_SESSIONS_PER_USER = 10\n',
     'MAX_PASSWORD_LENGTH = 256\nUSERNAME_RE = re.compile(r"^[a-z][a-z0-9_.-]{2,31}$")\n# admin: everything. viewer: watch live, browse and download recordings; no settings, no restart\n# or shutdown, cannot change any password (the administrator sets it on the Pi).\nROLES = ("admin", "viewer")\nTOKEN_BYTES = 32\nMAX_SESSIONS_PER_USER = 10\n'),
    ('app/auth.py',
     '    password_hash       TEXT NOT NULL,\n    created_at          INTEGER NOT NULL,\n    password_changed_at INTEGER NOT NULL\n);\nCREATE TABLE IF NOT EXISTS sessions (\n',
     "    password_hash       TEXT NOT NULL,\n    created_at          INTEGER NOT NULL,\n    password_changed_at INTEGER NOT NULL,\n    role                TEXTsudo bash ~/surveillance/deploy/install.shdploy/backup.sh deploy/restore.sh depl
updated app/__init__.py  (backup: app/__init__.py.bak)
updated app/auth.py  (backup: app/auth.py.bak)
updated app/web_auth.py  (backup: app/web_auth.py.bak)
updated tools/manage_users.py  (backup: tools/manage_users.py.bak)
updated web/templates/base.html  (backup: web/templates/base.html.bak)
updated web/templates/account.html  (backup: web/templates/account.html.bak)
updated web/templates/login.html  (backup: web/templates/login.html.bak)
updated deploy/harden.sh  (backup: deploy/harden.sh.bak)
updated tools/phase14_security_audit.py  (backup: tools/phase14_security_audit.py.bak)
created deploy/backup.sh
created deploy/restore.sh
created deploy/install-packages.sh
created .gitignore
created README.md
Update to 0.15.0 complete.
ff87c9ecdeae5486e1acbf7d53787496b03812b942f15c89d5c98e672bafaf5c  app/__init__.py
c12d9a92add9f5ff3e28d1dec3c0272983b8439198ff260b753e2d33648e08bc  app/auth.py
fbff1bf9b27783de06f83880dcabc82d072974717178b4de833729a774dc78f3  app/web_auth.py
99d1b5c9c7ecf46faea70485b26a3c6ed0815534de7991a17591c577d2a099b1  tools/manage_users.py
b296d00d4af8d85b450cdeae3f70b97e466b083d1f45d205d30d979661d4e2b7  web/templates/base.html
c2fb6f7ac6bdff3df0b00bb471f95817f91d50f0ef3840751510ef866a704fda  web/templates/account.html
38cbe040f55510ab3177b84964d0b1e28074b0c1991e331e66a99aaa5418856d  web/templates/login.html
ec804ba2f21acfa50729efc719f0b6fe135e5916021bb7d3cb7ff14e99c05fc0  deploy/harden.sh
5f47263ef4da38d48cc5f3fc349f4c814a658ca7ecfe9be4a98a0b654ba6c7e9  tools/phase14_security_audit.py
d48adf64f7180489e57a29e8ede08a34200310defefdf25ce73d5ee41cdb1405  deploy/backup.sh
e4f7a060a6ac1713338589b2560696ec856be53271e36e35cc332782473fae1f  deploy/restore.sh
014a080d5512348103b1be86f4487b93f3de1cc5fe63fe77f1a7628d5ab50dd0  deploy/install-packages.sh
0f86a1658556b0bf6fc9ce7df499a89bac963557bde1dda1eb04c50851bad410  .gitignore
78238838d830b1fa890daf9eaf4716e6bb06e2b694c307cdc7bfdc892ead2041  README.md

== Checking for copies started by hand
none

== Stopping the services (if they are running)

== Service user cctv
uid=999(cctv) gid=984(cctv) groups=984(cctv),44(video)

== Checking Python packages (as cctv)
found: picamera2 0.3.37, libcamera, cv2 4.10.0, numpy 2.2.4, av 14.2.0, simplejpeg 1.8.1, flask 3.1.1, waitress 3.0.2, argon2 25.1.0

== Installing the program into /opt/surveillance
version 0.15.0

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

Step 2:

ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/harden.sh ssh
sudo python3 /opt/surveillance/tools/phase14_security_audit.py

== Checking that ysak can log in with a key
keys in /home/ysak/.ssh/authorized_keys: 1

ERROR: no successful key login for ysak in the last 7 days. Log in once with your key (step 1), then run this again.
SSH server
  [FAIL] /etc/ssh/sshd_config.d/10-surveillance.conf is missing: run sudo bash ~/surveillance/deploy/harden.sh ssh
  [PASS] /etc/ssh/sshd_config reads sshd_config.d
  [FAIL] passwordauthentication yes (should be no)
  [PASS] kbdinteractiveauthentication no
  [FAIL] permitrootlogin without-password (should be no)
  [PASS] pubkeyauthentication yes
  [PASS] permitemptypasswords no
  [FAIL] x11forwarding yes (should be no)
  [FAIL] authenticationmethods any (should be publickey)
  [WARN] allowtcpforwarding yes
  [WARN] maxauthtries 6
  [WARN] allowusers (anyone)
  [PASS] Tailscale SSH off (OpenSSH with keys is used)
Firewall
  [PASS] surveillance-firewall: active, on at boot
  [PASS] incoming connections are dropped unless allowed
Listening ports (what the network could reach)
  [PASS] tcp :::22 (sshd): only through the tailnet (firewall)
  [PASS] tcp 0.0.0.0:22 (sshd): only through the tailnet (firewall)
  [PASS] tcp 100.91.247.39:443 (tailscaled): only through the tailnet (firewall)
  [PASS] tcp 100.91.247.39:55890 (tailscaled): blocked by the firewall (tailnet address)
  [PASS] tcp fd7a:115c:a1e0::5536:f728:40880 (tailscaled): blocked by the firewall (tailnet address)
  [PASS] tcp fd7a:115c:a1e0::5536:f728:443 (tailscaled): only through the tailnet (firewall)
  [PASS] udp *:36020 (avahi-daemon): blocked by the firewall
  [PASS] udp *:41641 (tailscaled): Tailscale's encrypted traffic
  [PASS] udp *:5353 (avahi-daemon): blocked by the firewall
  [PASS] udp 0.0.0.0:41641 (tailscaled): Tailscale's encrypted traffic
  [PASS] udp 0.0.0.0:48021 (avahi-daemon): blocked by the firewall
  [PASS] udp 0.0.0.0:5353 (avahi-daemon): blocked by the firewall
  [PASS] udp fe80::57a4:72e0:d20d:ca66:546 (NetworkManager): blocked by the firewall
sudo
  [PASS] sudo always asks for the password
  [PASS] members of the sudo group: ysak
Updates
  [PASS] automatic security updates on
  [PASS] apt-daily-upgrade.timer enabled
         last update run: 2026-09-24 22:43:48,772 INFO The list of kept packages can't be calculated in dry-run mode.
Camera services
  [PASS] surveillance-recorder: active, sandbox exposure 1.3 (0 best, 10 none)
  [PASS] surveillance-web: active, sandbox exposure 1.4 (0 best, 10 none)
  [PASS] surveillance-health: active, sandbox exposure 1.0 (0 best, 10 none)
  [PASS] /etc/polkit-1/rules.d/50-surveillance.rules: owned by root, not writable by others
File permissions
  [PASS] /opt/surveillance: owned by root and read-only to the services
  [PASS] /etc/surveillance: not readable by other users
  [PASS] /var/lib/surveillance: not readable by other users
  [PASS] /var/log/surveillance: not readable by other users
  [PASS] /usr/local/sbin/cctv-tool: root-owned, not writable by others
  [PASS] /etc/systemd/system/surveillance-recorder.service: root-owned, not writable by others
  [PASS] /etc/systemd/system/surveillance-health.service: root-owned, not writable by others
  [PASS] /etc/systemd/system/surveillance-web.service: root-owned, not writable by others
  [PASS] /etc/systemd/system/surveillance-firewall.service: root-owned, not writable by others
Accounts
  [PASS] accounts that can log in: root, ysak
  [PASS] root has no usable password
  [PASS] service user cctv: shell /usr/sbin/nologin

RESULT: PROBLEMS FOUND  (40 pass, 3 warn, 5 fail)
Skip the audit for ssh?

