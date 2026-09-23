ysak@ysak:~ $ python3 surveillance/tools/phase3_record_video.py 
[PASS] System clock: NTP synchronised
[PASS] Configure camera: main 1296x972 + lores 640x480 @ 15 fps, sensor mode (1296, 972)

Recording 60 s to /home/ysak/surveillance/test-recordings/phase3 (Ctrl+C stops early) ...
    10 s  frames=149  plain=1280 KiB
    20 s  frames=299  plain=2816 KiB
    30 s  frames=449  plain=4352 KiB
    40 s  frames=599  plain=5888 KiB
    50 s  frames=749  plain=7424 KiB

=== Checking files with ffprobe ===
[PASS] plain MP4: 20260923T132547Z_plain.mp4: H.264 High, 1296x972, 59.5 s, 892 frames, 9.3 MB
[PASS] fragmented MP4: 20260923T132547Z_fragmented.mp4: H.264 High, 1296x972, 59.5 s, 892 frames, 9.3 MB
[PASS] Frame count: 892 frames in 60.5 s = 15.0 fps (expected ~907)
[PASS] Timestamps: no gaps (largest step 66.7 ms)
[PASS] Keyframe interval: 2.0 s (requested 2 s)
[INFO] Bitrate: measured 1251 kbit/s, target 2500 kbit/s -> 0.56 GB/hour, 45 GB = 80 hours
[INFO] CPU: 12.1 % of one core for the whole recording process

=== Power-cut simulation (keep first 70% of each file) ===
[INFO] plain MP4 truncated: unplayable (expected: its index is written at the end)
[PASS] fragmented MP4 truncated: 41.6 s still playable
[INFO] CPU temperature: 50.6 C before, 51.6 C after

Metadata: /home/ysak/surveillance/test-recordings/phase3/20260923T132547Z_recording.json
PASS=8 WARN=0 FAIL=0 INFO=4
RESULT: RECORDING OK - now do the playback checks in the instructions.
