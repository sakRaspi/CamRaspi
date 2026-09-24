Step 1:
ysak@ysak:~/surveillance $ cd ~/surveillance
cat > update_to_0_14_0.py <<'PYEOF'
#!/usr/bin/env python3
"""Update the surveillance project from 0.13.2 to 0.14.0 (run from ~/surveillance)."""
import os, shutil, sys
from pathlib import Path

EDITS = [
    ('app/__init__.py',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.13.2"\n',
     '"""Raspberry Pi surveillance camera."""\n\n__version__ = "0.14.0"\n'),
    ('tools/phase12_tailscale_check.py',
     'HTTPS_TIMEOUT_S = 60\nKNOWN_PORTS = {\n    22: "SSH, restricted to the tailnet in Phase 14",\n    111: "rpcbind (an NFS helper this camera does not need): disable it, Phase 12 step 13",\n}\n',
     'HTTPS_TIMEOUT_S = 60\nKNOWN_PORTS = {\n    22: "SSH: limit it to the tailnet with the Phase 14 firewall",\n    111: "rpcbind (an NFS helper this camera does not need): disable it, Phase 12 step 13",\n}\n'),
    ('tools/phase12_tailscale_check.py',
     '\n    print("Listening ports")\n    sockets = listening_tcp()\n    web = [s for s in sockets if s[1] == web_port]\n',
     '\n    print("Listening ports")\n    firewall = subprocess.run(["systemctl", "is-active", "--quiet", "surveillance-firewall"],\n                              check=False).returncode == 0\n    sockets = listening_tcp()\n    web = [s forsudo bash ~/surveillance/deploy/install.sh4_security_audit.pyshd-hardening.conf 
updated app/__init__.py  (backup: app/__init__.py.bak)
updated tools/phase12_tailscale_check.py  (backup: tools/phase12_tailscale_check.py.bak)
created deploy/harden.sh
created deploy/firewall.nft
created deploy/surveillance-firewall.service
created deploy/sshd-hardening.conf
created deploy/apt-auto-upgrades.conf
created tools/phase14_security_audit.py
Update to 0.14.0 complete.
f44da4762ba8484e23b8e381d5efeeb018247aa8d05cfe8b0cabfbba66c28c6e  app/__init__.py
3496a0288c4c7bea3f9057c0ac10e5cb77f6b28d7746bb390dfde589fb7143c9  tools/phase12_tailscale_check.py
81e3f87b30633032b335b12c2ac12e067b246d697318239b8cfdc03629abfa75  deploy/harden.sh
557f1b22f6fcd4443ee3267221446090923206ae418935cd6cf32fe9f73402f1  deploy/firewall.nft
1dce7cdbadf58265098804c798fa93edba9ccaeea7f66c633a0d7560d4608086  deploy/surveillance-firewall.service
77759b2e214047c1aa25799e8cb7e36de08fc372dda80130faa82dc855f7bf32  deploy/sshd-hardening.conf
2decc65d5b15f3942f471f154b365bfbac0d389951e90c0fef6b56029672a3e1  deploy/apt-auto-upgrades.conf
ba2cf4fd9a42866fdf401dc8c54cd0a981c4115c694d89140f35c9bd55829567  tools/phase14_security_audit.py
[sudo] password for ysak: 

== Checking for copies started by hand
none

== Stopping the services (if they are running)

== Service user cctv
uid=999(cctv) gid=984(cctv) groups=984(cctv),44(video)

== Checking Python packages (as cctv)
found: picamera2 0.3.37, libcamera, cv2 4.10.0, numpy 2.2.4, av 14.2.0, simplejpeg 1.8.1, flask 3.1.1, waitress 3.0.2, argon2 25.1.0

== Installing the program into /opt/surveillance
version 0.14.0

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

Step 3:
ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/harden.sh ssh

== Checking that ysak can log in with a key
keys in /home/ysak/.ssh/authorized_keys: 1
a key login for ysak was seen

== Installing /etc/ssh/sshd_config.d/10-surveillance.conf
sshd reloaded (your current session stays open)

== Settings sshd now uses
  maxauthtries 3
  permitrootlogin no
  pubkeyauthentication yes
  passwordauthentication no
  kbdinteractiveauthentication no
  x11forwarding no
  allowtcpforwarding local
  allowusers ysak
  authenticationmethods publickey

Keep this session open. In a NEW terminal on your laptop, check:
  ssh ysak@cam01                                  -> logs in with your key
  ssh -o PubkeyAuthentication=no ysak@cam01       -> "Permission denied (publickey)"
If the first one fails, undo it from this session:  sudo rm /etc/ssh/sshd_config.d/10-surveillance.conf && sudo systemctl reload ssh

Step 3: Yup, it work exactly as intended

Step 4: ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/harden.sh firewall

== Checking that Tailscale is up (the only way in once the firewall is on)
Tailscale is connected

== Firewall ON (for 180 seconds unless you confirm)
  table inet cctv_filter {
  	chain input {
  		type filter hook input priority filter; policy drop;
  		iif "lo" accept
  		ct state established,related accept
  		ct state invalid drop
  		icmp type { destination-unreachable, time-exceeded, parameter-problem } accept
  		icmpv6 type { destination-unreachable, packet-too-big, time-exceeded, parameter-problem, nd-router-advert, nd-neighbor-solicit, nd-neighbor-advert } accept
  		icmp type echo-request limit rate 5/second burst 5 packets accept
  		icmpv6 type echo-request limit rate 5/second burst 5 packets accept
  		udp sport 67 udp dport 68 accept
  		udp sport 547 udp dport 546 accept
  		udp dport 41641 accept
  		iifname "tailscale0" tcp dport { 22, 443 } accept
  		counter packets 0 bytes 0 comment "dropped"
  	}
  }

It switches itself off at 22:43:48 unless you confirm. Now, in a NEW terminal on your laptop:
  ssh ysak@cam01                                                   (over the tailnet)
  sudo bash ~/surveillance/deploy/harden.sh firewall-confirm
If that new connection does not work, do nothing: the firewall removes itself.

ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/harden.sh firewall-confirm
Created symlink '/etc/systemd/system/sysinit.target.wants/surveillance-firewall.service' → '/etc/systemd/system/surveillance-firewall.service'.

== Firewall kept, and on at every boot
Emergency off (keyboard and screen on the Pi): sudo systemctl disable --now surveillance-firewall.service

Test-NetConnection : command not found in my Powershell

Step 5:
ysak@ysak:~/surveillance $ sudo bash ~/surveillance/deploy/harden.sh updates

== Installing unattended-upgrades
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  python3-distro-info
Suggested packages:
  bsd-mailx default-mta | mail-transport-agent needrestart powermgmt-base
The following NEW packages will be installed:
  python3-distro-info unattended-upgrades
0 upgraded, 2 newly installed, 0 to remove and 58 not upgraded.
Need to get 74.6 kB of archives.
After this operation, 368 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian trixie/main arm64 python3-distro-info all 1.13 [7,736 B]
Get:2 http://deb.debian.org/debian trixie/main arm64 unattended-upgrades all 2.12 [66.9 kB]
Fetched 74.6 kB in 0s (332 kB/s)          
Preconfiguring packages ...
Selecting previously unselected package python3-distro-info.
(Reading database ... 128603 files and directories currently installed.)
Preparing to unpack .../python3-distro-info_1.13_all.deb ...
Unpacking python3-distro-info (1.13) ...
Selecting previously unselected package unattended-upgrades.
Preparing to unpack .../unattended-upgrades_2.12_all.deb ...
Unpacking unattended-upgrades (2.12) ...
Setting up python3-distro-info (1.13) ...
Setting up unattended-upgrades (2.12) ...
Creating config file /etc/apt/apt.conf.d/20auto-upgrades with new version
Creating config file /etc/apt/apt.conf.d/50unattended-upgrades with new version
Created symlink '/etc/systemd/system/multi-user.target.wants/unattended-upgrades.service' → '/usr/lib/systemd/system/unattended-upgrades.service'.
Synchronizing state of unattended-upgrades.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable unattended-upgrades
Processing triggers for man-db (2.13.1-1) ...

== Configuration
  APT::Periodic::Update-Package-Lists "1";
  APT::Periodic::Unattended-Upgrade "1";
  Unattended-Upgrade::Origins-Pattern:: "origin=Debian,codename=${distro_codename},label=Debian";
  Unattended-Upgrade::Origins-Pattern:: "origin=Debian,codename=${distro_codename},label=Debian-Security";
  Unattended-Upgrade::Origins-Pattern:: "origin=Debian,codename=${distro_codename}-security,label=Debian-Security";
  NEXT                        LEFT LAST                        PASSED UNIT                    ACTIVATES
  Fri 2026-09-25 06:23:36 +08   7h Thu 2026-09-24 14:09:24 +08      - apt-daily-upgrade.timer apt-daily-upgrade.service
  Fri 2026-09-25 12:44:54 +08  14h Thu 2026-09-24 20:40:21 +08      - apt-daily.timer         apt-daily.service
  
  2 timers listed.
  Pass --all to see loaded but inactive timers, too.

== Trial run (installs nothing)
  Writing dpkg log to /var/log/unattended-upgrades/unattended-upgrades-dpkg.log
  /usr/bin/dpkg --status-fd 10 --no-triggers --unpack --auto-deconfigure /var/cache/apt/archives/xdg-dbus-proxy_0.1.6-1+deb13u3_arm64.deb 
  /usr/bin/dpkg --status-fd 10 --configure --pending 
  All upgrades installed
  The list of kept packages can't be calculated in dry-run mode.
Log of real runs: /var/log/unattended-upgrades/unattended-upgrades.log

Step 6:
ysak@ysak:~/surveillance $ sudo python3 /opt/surveillance/tools/phase14_security_audit.py
sudo cctv-tool phase12_tailscale_check
SSH server
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

RESULT: PROBLEMS FOUND  (39 pass, 3 warn, 4 fail)
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
  [PASS] 0.0.0.0:22: the firewall blocks it from the home network (open inside the tailnet)
         tailnet only : 100.91.247.39:443
         tailnet only : 100.91.247.39:55890
  [PASS] :::22: the firewall blocks it from the home network (open inside the tailnet)
         tailnet only : fd7a:115c:a1e0::5536:f728:443
         tailnet only : fd7a:115c:a1e0::5536:f728:40880
End-to-end HTTPS through the tailnet
  [PASS] HTTPS certificate valid for cam01.tail1c1671.ts.net (issued by Let's Encrypt, renews automatically, 89 days left)
  [PASS] https://cam01.tail1c1671.ts.net/login answers 200 through tailscale serve
  [PASS] browsers are told to always use HTTPS for this name (HSTS)
  [PASS] https://cam01.tail1c1671.ts.net/api/status without logging in answers 401 (expected 401)

RESULT: TAILNET ACCESS OK  (15 pass, 0 warn, 0 fail)

