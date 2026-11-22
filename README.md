## Третье ДЗ по ИПР

### 4 балла:

**Проверка:**

```
docker build -t counter-app .
docker run -d --name counter -e GREETING="Hi, supervisor!" -p 8080:8000 counter-app
curl -fsS http://127.0.0.1:8080/
curl -fsS -X POST http://127.0.0.1:8080/increment
curl -fsS -X POST http://127.0.0.1:8080/increment
curl -fsS http://127.0.0.1:8080/
curl -fsS -X POST http://127.0.0.1:8080/increment
curl -fsS http://127.0.0.1:8080/readyz
docker logs counter
```

**Вывод:**

```
{"greeting": "Hi, supervisor!", "count": 0, "hostname": "4ce21811a24b", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 1, "hostname": "4ce21811a24b", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 2, "hostname": "4ce21811a24b", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 2, "hostname": "4ce21811a24b", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 3, "hostname": "4ce21811a24b", "storage": "sqlite"}
{"status": "ready", "storage": "sqlite"}
2026-11-22 16:54:26,554 listening port=8000 storage=sqlite
2026-11-22 16:54:35,040 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-22 16:54:45,281 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
2026-11-22 16:54:49,569 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
2026-11-22 16:55:07,461 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-22 16:55:20,406 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
2026-11-22 16:55:29,038 http 172.17.0.1 "GET /readyz HTTP/1.1" 200 -
```

## 6 баллов:

**Проверка:**

Создаём контейнер
```
docker compose up -d --build
curl -fsS http://127.0.0.1:8080/
curl -fsS -X POST http://127.0.0.1:8080/increment
curl -fsS -X POST http://127.0.0.1:8080/increment
curl -fsS -X POST http://127.0.0.1:8080/increment
```
Сбрасываем и создаём заново.
```
docker compose down
docker compose up -d
curl -fsS http://127.0.0.1:8080/
curl -fsS -X POST http://127.0.0.1:8080/increment
```
Покажем, что порт и приветствие настраиваемы.
```
docker compose down
#сменим порт на 1337 и приветствие на "Hi, lord!" в .env
docker compose up -d
curl -fsS http://127.0.0.1:1337/
```

**Вывод:**
```
{"greeting": "Hi, supervisor!", "count": 0, "hostname": "4d6ef44e0b2c", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 1, "hostname": "4d6ef44e0b2c", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 2, "hostname": "4d6ef44e0b2c", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 3, "hostname": "4d6ef44e0b2c", "storage": "sqlite"}
[+] down 2/2
 ✔ Container hw_3_ipr-app-1 Removed                                                                                                                                               10.1s
 ✔ Network hw_3_ipr_default Removed 
[+] up 2/2
 ✔ Network hw_3_ipr_default Created                                                                                                                                                0.0s
 ✔ Container hw_3_ipr-app-1 Started
{"greeting": "Hi, supervisor!", "count": 3, "hostname": "c0d64b38576c", "storage": "sqlite"}
{"greeting": "Hi, supervisor!", "count": 4, "hostname": "c0d64b38576c", "storage": "sqlite"}
[+] down 2/2
 ✔ Container hw_3_ipr-app-1 Removed                                                                                                                                               10.1s
 ✔ Network hw_3_ipr_default Removed
[+] up 2/2
 ✔ Network hw_3_ipr_default Created                                                                                                                                                0.0s
 ✔ Container hw_3_ipr-app-1 Started
{"greeting": "Hi, lord!", "count": 4, "hostname": "2f284e388afa", "storage": "sqlite"}
```

## 8 баллов

**Проверка:**
```
docker compose down -v
docker compose up -d --build --wait
curl -fsS -X POST http://127.0.0.1:8080/increment
curl -fsS -X POST http://127.0.0.1:8080/increment
docker compose down
docker compose up -d --wait
docker compose ps
curl -fsS http://127.0.0.1:8080/
docker compose stop redis
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8080/readyz
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8080/healthz
curl -s -o /dev/null -w "%{http_code}\n" -X POST http://127.0.0.1:8080/increment
docker compose start redis
docker compose up -d --wait
curl -fsS http://127.0.0.1:8080/
curl -fsS -X POST http://127.0.0.1:8080/increment
```

**Вывод:**
Начинаем с curl после создания контейнера.
```
{"greeting": "Hi, supervisor!", "count": 1, "hostname": "bf64b8765d23", "storage": "redis"}

{"greeting": "Hi, supervisor!", "count": 2, "hostname": "bf64b8765d23", "storage": "redis"}

[+] down 3/3
 ✔ Container hw_3_ipr-app-1   Removed                                                 10.1s
 ✔ Container hw_3_ipr-redis-1 Removed                                                  0.1s
 ✔ Network hw_3_ipr_default   Removed     
[+] up 3/3
 ✔ Network hw_3_ipr_default   Created                                                  0.0s
 ✔ Container hw_3_ipr-redis-1 Healthy                                                 30.2s
 ✔ Container hw_3_ipr-app-1   Healthy    

NAME               IMAGE              COMMAND                  SERVICE   CREATED          STATUS                    PORTS
hw_3_ipr-app-1     hw_3_ipr-app       "python app.py"          app       37 seconds ago   Up 36 seconds (healthy)   0.0.0.0:8080->8000/tcp, [::]:8080->8000/tcp
hw_3_ipr-redis-1   redis:7.4-alpine   "docker-entrypoint.s…"   redis     37 seconds ago   Up 36 seconds (healthy)   6379/tcp

{"greeting": "Hi, supervisor!", "count": 2, "hostname": "08871bd221f8", "storage": "redis"}

[+] stop 1/1
 ✔ Container hw_3_ipr-redis-1 Stopped      

503

200

503

[+] start 1/1
 ✔ Container hw_3_ipr-redis-1 Started    
[+] up 2/2
 ✔ Container hw_3_ipr-app-1   Healthy                                                  0.5s
 ✔ Container hw_3_ipr-redis-1 Healthy      

{"greeting": "Hi, supervisor!", "count": 2, "hostname": "08871bd221f8", "storage": "redis"}

{"greeting": "Hi, supervisor!", "count": 3, "hostname": "08871bd221f8", "storage": "redis"}
```