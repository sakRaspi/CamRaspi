ysak@ysak:~ $ python3 surveillance/tools/phase2_still_capture.py 
Camera: ov5647. Saving to /home/ysak/surveillance/snapshots/phase2

Capturing full: full sensor still 2592x1944 ...
  [PASS] 20260923T120504Z_full.jpg: 2592x1944, 1043 KiB, exposure 66663 us, gain 8.00
Capturing wide: full field of view 1296x972 (binned) ...
  [PASS] 20260923T120504Z_wide.jpg: 1296x972, 175 KiB, exposure 66565 us, gain 8.00
Capturing crop1080: 1920x1080 sensor mode ...
  [PASS] 20260923T120504Z_crop1080.jpg: 1920x1080, 288 KiB, exposure 55388 us, gain 4.00
Capturing motion_view: motion detector view 640x480 ...
  [PASS] 20260923T120504Z_motion_view.png: 640x480, 111 KiB, exposure 59978 us, gain 4.06, brightness 119/255

Metadata: /home/ysak/surveillance/snapshots/phase2/20260923T120504Z_captures.json
RESULT: STILL CAPTURE OK - Phase 2 complete.

ysak@ysak:~ $ ls -la ~/surveillance/snapshots/phase2/
total 1636
drwxrwxr-x 2 ysak ysak    4096 Sep 23 20:05 .
drwxrwxr-x 3 ysak ysak    4096 Sep 23 20:05 ..
-rw-rw-r-- 1 ysak ysak    1872 Sep 23 20:05 20260923T120504Z_captures.json
-rw-rw-r-- 1 ysak ysak  295403 Sep 23 20:05 20260923T120504Z_crop1080.jpg
-rw-rw-r-- 1 ysak ysak 1068274 Sep 23 20:05 20260923T120504Z_full.jpg
-rw-rw-r-- 1 ysak ysak  113756 Sep 23 20:05 20260923T120504Z_motion_view.png
-rw-rw-r-- 1 ysak ysak  179318 Sep 23 20:05 20260923T120504Z_wide.jpg

The output is perfectly fine
