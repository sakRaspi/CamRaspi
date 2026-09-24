Step 7:
cat > ~/surveillance/tools/phase12_tailscale_check.py <<'EOF'
#!/usr/bin/env python3
"""Phase 12: check that the camera is reachable only through the tailnet, over HTTPS.

    python3 ~/surveillance/tools/phase12_tailscale_check.py

Read-only. Checks the Tailscale connection, the HTTPS certificate, the `tailscale serve`
mapping to the local web interface, that Funnel (public internet sharing) is off, which
ports the Pi listens on, and finally fetches the login page through the tailnet address.
"""
from __future__ import annotations

import argparse
import ipaddress
import json
import shutil
import socket
import ssl
import subprocess
import sys
import time
from datetime import datetime, timezone
from pathlib import Path
from urllib.parse import urlsplit

sys.path.insert(0, str(Path(__file__).resolve().parent.parent))

from app.config import DEFAULT_CONFIG_PATH, ConfigError, load_settings  # noqa: E402

TAILNET_V4 = ipaddress.ip_network("100.64.0.0/10")
TAILNET_V6 = ipaddress.ip_network("fd7a:115c:a1e0::/48")
CAMERA_TAG = "tag:camera"
HTTPS_TIMEOUT_S = 60

results: list[str] = []


def report(status: str, text: str) -> None:
    results.append(status)
    print(f"  [{status}] {text}")


def run_json(cmd: list[str]) -> dict | None:
    try:
        out = subprocess.run(cmd, capture_output=True, text=True, timeout=20)
    except (OSError, subprocess.TimeoutExpired) as exc:
        report("FAIL", f"{' '.join(cmd)} failed: {exc}")
        return None
    if out.returncode != 0:
        report("FAIL", f"{' '.join(cmd)} failed: {(out.stderr or out.stdout).strip()[:300]}")
        return None
    try:
        return json.loads(out.stdout or "{}")
    except ValueError:
        report("FAIL", f"{' '.join(cmd)} did not return JSON")
        return None


def is_loopback(addr: str) -> bool:
    try:
        return ipaddress.ip_address(addr).is_loopback
    except ValueError:
        return addr == "localhost"


def is_tailnet(addr: str) -> bool:
    try:
        ip = ipaddress.ip_address(addr)
    except ValueError:
        return False
    return ip in (TAILNET_V4 if ip.version == 4 else TAILNET_V6)


def listening_tcp() -> list[tuple[str, int, str]]:
    """(address, port, process) of every listening TCP socket."""
    try:
        out = subprocess.run(["ss", "-Hltnp"], capture_output=True, text=True, timeout=10).stdout
    except (OSError, subprocess.TimeoutExpired):
        return []
    sockets = []
    for line in out.splitlines():
        parts = line.split()
        if len(parts) < 4:
            continue
        local = parts[3]
        addr, _, port = local.rpartition(":")
        addr = addr.strip("[]").split("%")[0]
        process = ""
        if "users:((" in line:
            process = line.split('users:(("', 1)[1].split('"', 1)[0]
        if port.isdigit():
            sockets.append((addr, int(port), process))
    return sockets


def check_https(dns_name: str, ip: str, cafile: str | None) -> None:
    context = ssl.create_default_context(cafile=cafile)
    deadline = time.monotonic() + HTTPS_TIMEOUT_S
    while True:
        try:
            with socket.create_connection((ip, 443), timeout=15) as raw:
                with context.wrap_socket(raw, server_hostname=dns_name) as tls:
                    cert = tls.getpeercert()
                    responses = {}
                    for path in ("/login", "/api/status"):
                        tls.sendall(f"GET {path} HTTP/1.1\r\nHost: {dns_name}\r\nUser-Agent: phase12-check\r\n"
                                    f"Connection: {'close' if path == '/api/status' else 'keep-alive'}\r\n\r\n"
                                    .encode())
                        responses[path] = read_response(tls)
            break
        except ssl.SSLCertVerificationError as exc:
            report("FAIL", f"HTTPS certificate for {dns_name} is not valid: {exc.verify_message}")
            return
        except (OSError, ssl.SSLError) as exc:
            if time.monotonic() > deadline:
                report("FAIL", f"cannot reach https://{dns_name} ({ip}:443): {exc}")
                return
            print(f"         waiting for HTTPS on {ip}:443 ({exc}); the first certificate can take a minute ...")
            time.sleep(5)
    expires = datetime.strptime(cert["notAfter"], "%b %d %H:%M:%S %Y %Z").replace(tzinfo=timezone.utc)
    days = (expires - datetime.now(timezone.utc)).days
    report("PASS", f"HTTPS certificate valid for {dns_name} (issued by "
                   f"{dict(x[0] for x in cert['issuer']).get('organizationName', '?')}, renews automatically, "
                   f"{days} days left)")
    status, headers = responses["/login"]
    if status == 200:
        report("PASS", f"https://{dns_name}/login answers 200 through tailscale serve")
    elif status == 400:
        report("FAIL", f"https://{dns_name}/login answers 400: the web interface does not accept this name. "
                       f"Set web.https_hostname (step 7) and restart the web interface")
    else:
        report("FAIL", f"https://{dns_name}/login answers {status}")
    if "strict-transport-security" in headers:
        report("PASS", "browsers are told to always use HTTPS for this name (HSTS)")
    elif status == 200:
        report("WARN", "no HSTS header: is the web interface updated to 0.12.0 and restarted?")
    status, _headers = responses["/api/status"]
    report("PASS" if status == 401 else "FAIL",
           f"https://{dns_name}/api/status without logging in answers {status} (expected 401)")


def read_response(tls: ssl.SSLSocket) -> tuple[int, dict[str, str]]:
    data = b""
    while b"\r\n\r\n" not in data:
        chunk = tls.recv(4096)
        if not chunk:
            break
        data += chunk
    head, _, body = data.partition(b"\r\n\r\n")
    lines = head.decode("latin-1").split("\r\n")
    status = int(lines[0].split()[1]) if lines and len(lines[0].split()) > 1 else 0
    headers = {k.strip().lower(): v.strip() for k, _, v in (line.partition(":") for line in lines[1:])}
    length = int(headers.get("content-length", "0") or 0)
    while len(body) < length:
        chunk = tls.recv(4096)
        if not chunk:
            break
        body += chunk
    return status, headers


def main() -> int:
    parser = argparse.ArgumentParser(description="Phase 12: Tailscale / HTTPS check (read-only)")
    parser.add_argument("--config", type=Path, default=DEFAULT_CONFIG_PATH)
    parser.add_argument("--tailscale", default="tailscale", help=argparse.SUPPRESS)
    parser.add_argument("--cafile", help=argparse.SUPPRESS)
    args = parser.parse_args()
    try:
        settings, _ = load_settings(args.config, create_if_missing=False)
    except ConfigError as exc:
        print(f"Configuration error: {exc}", file=sys.stderr)
        return 2
    web_port = settings.web.port

    print("Tailscale")
    if not shutil.which(args.tailscale):
        report("FAIL", "tailscale is not installed (step 2)")
        return finish()
    status = run_json([args.tailscale, "status", "--json"])
    if status is None:
        return finish()
    report("PASS" if status.get("BackendState") == "Running" else "FAIL",
           f"Tailscale state: {status.get('BackendState')} (version {status.get('Version', '?').split('-')[0]})")
    me = status.get("Self") or {}
    dns_name = (me.get("DNSName") or "").rstrip(".").lower()
    ips = [ip for ip in me.get("TailscaleIPs") or [] if ":" not in ip]
    tailnet = status.get("CurrentTailnet") or {}
    print(f"         this Pi in the tailnet: {dns_name or '?'}  {' '.join(me.get('TailscaleIPs') or [])}")
    if not dns_name or not ips:
        report("FAIL", "no tailnet name or address yet: finish `sudo tailscale up` (step 3)")
        return finish()
    report("PASS" if tailnet.get("MagicDNSEnabled") else "FAIL",
           "MagicDNS " + ("on" if tailnet.get("MagicDNSEnabled") else "off: turn it on in the admin console, DNS page"))
    tags = list(me.get("Tags") or [])
    report("PASS" if CAMERA_TAG in tags else "WARN",
           f"device tags: {', '.join(tags) or 'none'}" + ("" if CAMERA_TAG in tags else
                                                          f" (add {CAMERA_TAG} in the admin console, step 5)"))
    expiry = me.get("KeyExpiry")
    if expiry:
        report("WARN", f"device key expires {expiry[:10]}: the camera would drop off the tailnet then "
                       f"(tagging it disables expiry)")
    else:
        report("PASS", "device key does not expire")
    cert_domains = [d.rstrip(".").lower() for d in status.get("CertDomains") or []]
    report("PASS" if dns_name in cert_domains else "FAIL",
           "HTTPS certificates " + ("enabled" if dns_name in cert_domains
                                    else "not enabled: admin console, DNS page, Enable HTTPS (step 6)"))

    print("tailscale serve")
    serve = run_json([args.tailscale, "serve", "status", "--json"]) or {}
    handler = (((serve.get("Web") or {}).get(f"{dns_name}:443") or {}).get("Handlers") or {}).get("/") or {}
    proxy = handler.get("Proxy", "")
    target = urlsplit(proxy if "://" in proxy else f"http://{proxy}") if proxy else None
    if target and target.scheme == "http" and target.hostname in ("127.0.0.1", "localhost") \
            and target.port == web_port and (serve.get("TCP") or {}).get("443", {}).get("HTTPS"):
        report("PASS", f"https://{dns_name} -> {proxy}")
    else:
        report("FAIL", f"https://{dns_name}/ is not forwarded to http://127.0.0.1:{web_port} "
                       f"(found {proxy or 'nothing'}); run the serve command in step 8")
    funnel = [hp for hp, on in (serve.get("AllowFunnel") or {}).items() if on]
    if funnel:
        report("FAIL", f"Funnel is ON for {', '.join(funnel)}: the camera is on the public internet! "
                       f"Run: sudo tailscale funnel reset")
    else:
        report("PASS", "Funnel is off: nothing is shared outside the tailnet")
    others = sorted(p for p in (serve.get("TCP") or {}) if p != "443")
    if others:
        report("WARN", f"tailscale serve also publishes port(s) {', '.join(others)}")

    print("Web interface")
    expected = settings.web.https_hostname
    report("PASS" if expected == dns_name else "FAIL",
           f"web.https_hostname = {expected or '(empty)'}" + ("" if expected == dns_name else
           f"; run: python3 ~/surveillance/tools/set_setting.py web.https_hostname {dns_name}  (then restart it)"))

    print("Listening ports")
    sockets = listening_tcp()
    web = [s for s in sockets if s[1] == web_port]
    if web and all(is_loopback(a) for a, _p, _n in web):
        report("PASS", f"web interface port {web_port} listens on this Pi only (127.0.0.1)")
    elif web:
        report("FAIL", f"port {web_port} is reachable from the network: {web}")
    else:
        report("WARN", f"nothing listens on port {web_port}: is the web interface running?")
    for addr, port, name in sorted(set(sockets)):
        if is_loopback(addr) or port == web_port:
            continue
        who = f" ({name})" if name else ""
        if is_tailnet(addr):
            print(f"         tailnet only : {addr}:{port}{who}")
        else:
            label = "SSH, restricted to the tailnet in Phase 14" if port == 22 else "check what this is"
            report("WARN", f"reachable from the home network: {addr or '*'}:{port}{who}: {label}")

    print("End-to-end HTTPS through the tailnet")
    check_https(dns_name, ips[0], args.cafile)
    return finish()


def finish() -> int:
    fails, warns = results.count("FAIL"), results.count("WARN")
    verdict = "TAILNET ACCESS NOT READY" if fails else "TAILNET ACCESS OK"
    print(f"\nRESULT: {verdict}  ({results.count('PASS')} pass, {warns} warn, {fails} fail)")
    return 1 if fails else 0


if __name__ == "__main__":
    sys.exit(main())
EOF
cd ~/surveillance && sha256sum app/__init__.py app/auth.py app/config.py app/main.py app/status.py app/web.py app/web_auth.py app/web_main.py app/web_settings.py tools/set_setting.py web/static/app.js tools/phase12_tailscale_check.py

Step 8 and 9:
ysak@ysak:~/surveillance $ tailscale serve status
https://cam01.tail1c1671.ts.net (tailnet only)
|-- / proxy http://127.0.0.1:8080

ysak@ysak:~/surveillance $ python3 ~/surveillance/tools/phase12_tailscale_check.py
Tailscale
  [PASS] Tailscale state: Running (version 1.102.4)
         this Pi in the tailnet: cam01.tail1c1671.ts.net  100.91.247.39 fd7a:115c:a1e0::5536:f728
  [PASS] MagicDNS on
  [PASS] device tags: tag:camera
  [WARN] device key expires 2027-03-23: the camera would drop off the tailnet then (tagging it disables expiry)
  [PASS] HTTPS certificates enabled
tailscale serve
  [PASS] https://cam01.tail1c1671.ts.net -> http://127.0.0.1:8080
  [PASS] Funnel is off: nothing is shared outside the tailnet
Web interface
  [PASS] web.https_hostname = cam01.tail1c1671.ts.net
Listening ports
  [PASS] web interface port 8080 listens on this Pi only (127.0.0.1)
  [WARN] reachable from the home network: 0.0.0.0:22: SSH, restricted to the tailnet in Phase 14
  [WARN] reachable from the home network: 0.0.0.0:111: check what this is
         tailnet only : 100.91.247.39:443
         tailnet only : 100.91.247.39:55890
  [WARN] reachable from the home network: :::22: SSH, restricted to the tailnet in Phase 14
  [WARN] reachable from the home network: :::111: check what this is
         tailnet only : fd7a:115c:a1e0::5536:f728:443
         tailnet only : fd7a:115c:a1e0::5536:f728:40880
End-to-end HTTPS through the tailnet
  [PASS] HTTPS certificate valid for cam01.tail1c1671.ts.net (issued by Let's Encrypt, renews automatically, 89 days left)
  [PASS] https://cam01.tail1c1671.ts.net/login answers 200 through tailscale serve
  [PASS] browsers are told to always use HTTPS for this name (HSTS)
  [PASS] https://cam01.tail1c1671.ts.net/api/status without logging in answers 401 (expected 401)

RESULT: TAILNET ACCESS OK  (12 pass, 5 warn, 0 fail)

4. Yes, after the Tailscale is off, i can't access the internet anymore

Step 12:
ysak@ysak:~/surveillance $ ping -c 3 192.168.0.26
PING 192.168.0.26 (192.168.0.26) 56(84) bytes of data.

--- 192.168.0.26 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2049ms

ysak@ysak:~/surveillance $ ^C
ysak@ysak:~/surveillance $ top -b -n 3 -d 5 | grep -E "python3|tailscaled"
  13162 ysak      20   0 2502196 193024 128156 S   9.1   5.0   1:58.54 python3
  13194 ysak      20   0  601300  70212  13632 S   9.1   1.8   0:14.11 python3
  13162 ysak      20   0 2502196 193024 128156 S  16.1   5.0   1:59.35 python3
  13194 ysak      20   0  601300  70212  13632 S   0.8   1.8   0:14.15 python3
  13162 ysak      20   0 2502196 193024 128156 S  12.3   5.0   1:59.97 python3
  13194 ysak      20   0  601300  70212  13632 S   0.8   1.8   0:14.19 python3
