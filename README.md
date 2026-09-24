screenshot

Step 6:
ysak@ysak:~ $ curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8080/
200
ysak@ysak:~ $ curl -s -o /dev/null -w "%{http_code}\n" -H "Host: evil.example.com" http://127.0.0.1:8080/    # 400
ss -ltnp | grep 8080                                   # must show 127.0.0.1:8080, not 0.0.0.0
python3 tools/set_setting.py web.host 0.0.0.0          # must be refused

400
LISTEN 0      1024       127.0.0.1:8080      0.0.0.0:*    users:(("python3",pid=5652,fd=7))
python3: can't open file '/home/ysak/tools/set_setting.py': [Errno 2] No such file or directory

My laptop unable to connect
