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
  "tests": [
    {"src": "YOUR-LOGIN@example.com",
     "accept": ["tag:camera:443", "tag:camera:22"],
     "deny": ["tag:camera:80", "tag:camera:8080"]},
    // The camera must not be able to open anything on your own devices.
    {"src": "tag:camera",
     "deny": ["YOUR-LOGIN@example.com:22", "YOUR-LOGIN@example.com:443",
              "YOUR-LOGIN@example.com:445", "YOUR-LOGIN@example.com:3389"]},
    {"src": "tag:camera", "proto": "icmp", "deny": ["YOUR-LOGIN@example.com:0"]}
  ]
