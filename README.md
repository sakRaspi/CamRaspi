{
  // Only admins (you) may mark a device as a camera.
  "tagOwners": {
    "tag:camera": ["autogroup:admin"]
  },
  "grants": [
    // Your own laptop/phone may reach each other as before.
    {"src": ["autogroup:member"], "dst": ["autogroup:self"], "ip": ["*"]},
    // Your devices may open the camera's web page and SSH into it. Nothing else.
    {"src": ["autogroup:member"], "dst": ["tag:camera"], "ip": ["tcp:443", "tcp:22"]}
    // No rule has the camera as "src": it cannot start a connection to any of your devices.
  ],
  "tests": [
    {"src": "YOUR-LOGIN@example.com",
     "accept": ["tag:camera:443", "tag:camera:22"],
     "deny": ["tag:camera:80", "tag:camera:8080"]}
  ]
}
