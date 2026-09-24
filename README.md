Step 6:
ysak@ysak:~/surveillance $ curl -s -o /dev/null -w "%{http_code}\n" "http://127.0.0.1:8080/recordings/../../../etc/passwd" 
404
ysak@ysak:~/surveillance $ curl -s -o /dev/null -w "%{http_code}\n" -r 0-99 http://127.0.0.1:8080/recordings/1/video 
206
ysak@ysak:~/surveillance $ ls /tmp/surveillance-download-* 2>/dev/null | wc -l 
0
