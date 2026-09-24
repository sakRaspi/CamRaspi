ysak@ysak:~/surveillance $ python3 tools/manage_users.py list 
admin                created 2026-09-24 17:50  password changed 2026-09-24 17:50  sessions 1  failed logins 5 LOCKED until 2026-09-24 17:55
ysak@ysak:~/surveillance $ python3 tools/manage_users.py unlock admin
Lockouts cleared.
ysak@ysak:~/surveillance $ curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8080/api/status            # 401
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8080/live/stream           # 401
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8080/recordings/1/video    # 401
curl -s -o /dev/null -w "%{http_code}\n" -X POST -H "Origin: https://evil.example" http://127.0.0.1:8080/login   # 403
python3 tools/manage_users.py log | tail -10
401
401
401
403
2026-09-24 17:53  admin                password_change_failed   wrong current password
2026-09-24 17:54  admin                login                    
2026-09-24 17:54  admin                logout                   
2026-09-24 17:55  admin                login_failed             
2026-09-24 17:55  admin                login_failed             
2026-09-24 17:55  admin                login_failed             
2026-09-24 17:55  admin                login_failed             
2026-09-24 17:55  admin                login_failed             
2026-09-24 17:55  admin                login_blocked            username locked
2026-09-24 17:55  admin                unlocked                 by cli
