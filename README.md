Step 12:
ysak@ysak:~/surveillance $ cd ~/surveillance
cat > update_phase12_checker.py <<'EOF'
#!/usr/bin/env python3
"""Update the surveillance project Phase 12 checker fixes (run from ~/surveillance)."""
import shutil, sys
from pathlib import Path

EDITS = [
    ('tools/phase12_tailscale_check.py',
     'CAMERA_TAG = "tag:camera"\nHTTPS_TIMEOUT_S = 60\n\nresults: list[str] = []\n',
     'CAMERA_TAG = "tag:camera"\nHTTPS_TIMEOUT_S = 60\nKNOWN_PORTS = {\n    22: "SSH, restricted to the tailnet in Phase 14",\n    111: "rpcbind (an NFS helper this camera does not need): disable it, Phase 12 step 13",\n}\n\nresults: list[str] = []\n'),
    ('tools/phase12_tailscale_check.py',
     '    expiry = me.get("KeyExpiry")\n    if expiry:\n        report("WARN", f"device key expires {expiry[:10]}: the camera would drop off the tailnet then "\n                       f"(tagging it disables expiry)")\n    else:\n        report("PASS", "device key does not expire")\n',
     '    expiry = me.get("KeyExpiry")\n    if expiry:\n        report("WARN", f"device key expires {expiry[:10]}: the camera would drop off the tailnet then; "\n                       f"admin console, Machines, ... menu of this device, Disable key expiry")\n    else:\n        report("PASS", "device key does not expirey web/static/app.js tools/phase12_tailscale_check.pytings.py tools/set_setting.p
updated tools/phase12_tailscale_check.py  (backup: tools/phase12_tailscale_check.py.bak)
Checker updated.
1efa1678f66b2b0ed34dfdf16512f8fa61c2885e4ec98a2fe72d84a498c6852d  app/__init__.py
998c6a9c73cd4e5b64a5ac0239351a307e0ec9e02d57837ad32243d4ee4236af  app/auth.py
e7d31faf82f80a97a06f9a2ebbd342c919dc5c8b9c020acd5eb44f7d6133de44  app/config.py
714ba5f4f8f70b08e161e6007a642c85c427d5f620772ff393b9fd0f19aadd62  app/main.py
ae7b0d98e0a2ab74a481647c9f82e63e2c2bfaefa4337abfacd5737040635355  app/status.py
5b0c272c7b5f0f1b997ed9e73188103a58da3bcb1e6d492905823a816634bdef  app/web.py
0341425f0be9009e6f4d218ac446f1d5a27b8bbd86d97b9d04a49932d1cfed3c  app/web_auth.py
b2873a18d7a399851cd0a7cf1a27a89fadb9dd29d5cf5b8b890bc44872742185  app/web_main.py
cef58b84b320e8e2c0871104cc7d679d6b067a7e5dc8dba0fb12915ed7981758  app/web_settings.py
6541409bf57715b98c716e361668a5fe7395cf78bd23cb07fb12de0572589272  tools/set_setting.py
db63f25f64ac7ed1c2cd11c9a285d28b8f754e18cebc17c150cc2505a8b91e36  web/static/app.js
ffcbb36d4d77b10ec28d97b2507af593f2cad9846eb606faa4bc258a113db5fa  tools/phase12_tailscale_check.py

Step 13:
ysak@ysak:~/surveillance $ systemctl status rpcbind --no-pager | head -3
● rpcbind.service - RPC bind portmap service
     Loaded: loaded (/usr/lib/systemd/system/rpcbind.service; enabled; preset: enabled)
     Active: active (running) since Thu 2026-09-24 00:39:34 +08; 20h ago
ysak@ysak:~/surveillance $ sudo systemctl disable --now rpcbind.service rpcbind.socket
sudo systemctl mask rpcbind.service rpcbind.socket
sudo ss -ltnup | grep ':111 ' || echo "port 111 closed"
[sudo] password for ysak: 
Synchronizing state of rpcbind.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install disable rpcbind
Removed '/etc/systemd/system/multi-user.target.wants/rpcbind.service'.
Removed '/etc/systemd/system/sockets.target.wants/rpcbind.socket'.
Disabling 'rpcbind.service', but its triggering units are still active:
rpcbind.socket
Created symlink '/etc/systemd/system/rpcbind.service' → '/dev/null'.
Created symlink '/etc/systemd/system/rpcbind.socket' → '/dev/null'.
port 111 closed

Step 15:
ysak@ysak:~/surveillance $ tailscale status
100.91.247.39  cam01            cam01.tail1c1671.ts.net  linux    -                                                        
100.85.224.6   desktop-nkvn22o  liang787878@             windows  active; direct 192.168.0.26:41641, tx 1079148 rx 141884  
100.94.18.52   poco-f6          liang787878@             android  offline, last seen 15m ago  

ysak@ysak:~/surveillance $ ping -c 3 192.168.0.26
PING 192.168.0.26 (192.168.0.26) 56(84) bytes of data.

--- 192.168.0.26 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2052ms

Step 16:
ysak@ysak:~/surveillance $ top -b -n 3 -d 5 -p "$(pgrep -d, -x tailscaled),$(pgrep -d, -f 'app.main|app.web_main')"
top - 20:47:54 up  6:49,  7 users,  load average: 0.42, 0.52, 0.55
Tasks:   3 total,   0 running,   3 sleeping,   0 stopped,   0 zombie
%Cpu(s):  7.0 us,  2.3 sy,  0.0 ni, 90.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st 
MiB Mem :   3789.8 total,    188.6 free,   1425.6 used,   2379.7 buff/cache     
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2364.1 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  12919 root      20   0 1420916  52660  23064 S   9.1   1.4   0:32.65 tailsca+
  13162 ysak      20   0 2493940 192704 128156 S   9.1   5.0   5:25.07 python3
  13194 ysak      20   0  601300  77288  13632 S   0.0   2.0   0:18.82 python3

top - 20:47:59 up  6:49,  7 users,  load average: 0.39, 0.51, 0.55
Tasks:   3 total,   0 running,   3 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3.8 us,  2.0 sy,  0.0 ni, 94.0 id,  0.1 wa,  0.0 hi,  0.1 si,  0.0 st 
MiB Mem :   3789.8 total,    191.2 free,   1422.8 used,   2379.9 buff/cache     
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2366.9 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  13162 ysak      20   0 2493940 192704 128156 S  11.0   5.0   5:25.62 python3
  12919 root      20   0 1420916  53064  23064 S   3.0   1.4   0:32.80 tailsca+
  13194 ysak      20   0  601300  77288  13632 S   0.6   2.0   0:18.85 python3

top - 20:48:04 up  6:49,  7 users,  load average: 0.36, 0.50, 0.55
Tasks:   3 total,   0 running,   3 sleeping,   0 stopped,   0 zombie
%Cpu(s):  4.2 us,  3.1 sy,  0.0 ni, 92.4 id,  0.1 wa,  0.0 hi,  0.3 si,  0.0 st 
MiB Mem :   3789.8 total,    192.5 free,   1420.8 used,   2380.6 buff/cache     
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2369.0 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  13162 ysak      20   0 2493940 192708 128156 S  19.8   5.0   5:26.61 python3
  12919 root      20   0 1420916  53628  23064 S   4.6   1.4   0:33.03 tailsca+
  13194 ysak      20   0  601300  77288  13632 S   0.6   2.0   0:18.88 python3

  Step 17:
  ysak@ysak:~/surveillance $ python3 ~/surveillance/tools/phase12_tailscale_check.py
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
