# 6단계 전체 실행 기록 — 공개용 사본

학습자가 제공한 첨부 텍스트의 실행 순서를 보존했다. 개인 컴퓨터명을 마스킹하고 줄바꿈을 LF로 정규화했다. 오류 기록도 삭제하지 않았다.

- 원본: 붙여넣은 텍스트.txt
- 원본 파일의 SHA-256:

```text
07b70e474767ea780d9e2e3fdb331f5653a9d7506e85e4d909a493df2d6101b3
```

두 브라우저 화면은 대화에서 별도로 확인됐다. 이 텍스트 기록 자체에 이미지가 들어 있는 것은 아니다.

## 실제 실행 기록

```text
PS C:\Users\user> wsl -d Ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED             STATUS                    PORTS                                     NAMES
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   About an hour ago   Up About an hour          0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   3 hours ago         Up 3 hours                                                          mission4-ubuntu
9464f39b44cc   ubuntu           "bash"                   2 days ago          Exited (129) 2 days ago                                             mission1-ubuntu
6332a18f60cc   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               charming_rubin
41cf1df1c148   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               blissful_hofstadter
86997a6cd49f   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               bold_banzai
0782527e408c   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               strange_mayer
abe4a0c49198   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               elated_faraday
eb43b4fbd3a9   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               elated_easley
004ddf4c8a0f   hello-world      "/hello"                 3 days ago          Exited (0) 3 days ago                                               objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ cd ~
user@[컴퓨터명]:~$ cd mission-step6
-bash: cd: mission-step6: No such file or directory
user@[컴퓨터명]:~$ pwd
/home/user
user@[컴퓨터명]:~$ mkdir app
user@[컴퓨터명]:~$ cp ~/mission-step5/app/index.html app/index.html
user@[컴퓨터명]:~$ cat app/index.html
<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>My Docker Web Server</h1>
    <p>Mission 5: Nginx custom image</p>
    <p>This page was built with Docker.</p>
</body>
</html>
user@[컴퓨터명]:~$ ocker run -d --name mission-web-bind -p 8081:80 --mount type=bind,source=/home/user/mission-step6/app,target=/usr/share/nginx/html,readonly mission-web:v1
Command 'ocker' not found, did you mean:
  command 'docker' from snap docker (29.8.0)
  command 'docker' from deb docker.io (29.1.3-0ubuntu4.1)
  command 'docker' from deb podman-docker (5.7.0+ds2-3build1)
See 'snap info <snapname>' for additional versions.
user@[컴퓨터명]:~$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED             STATUS             PORTS                                     NAMES
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   About an hour ago   Up About an hour   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   3 hours ago         Up 3 hours                                                   mission4-ubuntu
user@[컴퓨터명]:~$ docker inspect --format '{{json .Mounts}}' mission-web-bind

error: no such object: mission-web-bind
user@[컴퓨터명]:~$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED             STATUS                    PORTS                                     NAMES
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   About an hour ago   Up About an hour          0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   3 hours ago         Up 3 hours                                                          mission4-ubuntu
9464f39b44cc   ubuntu           "bash"                   2 days ago          Exited (129) 2 days ago                                             mission1-ubuntu
6332a18f60cc   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               charming_rubin
41cf1df1c148   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               blissful_hofstadter
86997a6cd49f   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               bold_banzai
0782527e408c   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               strange_mayer
abe4a0c49198   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               elated_faraday
eb43b4fbd3a9   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                               elated_easley
004ddf4c8a0f   hello-world      "/hello"                 3 days ago          Exited (0) 3 days ago                                               objective_tu
user@[컴퓨터명]:~$ ls -l /home/user/mission-step6/app/index.html
ls: cannot access '/home/user/mission-step6/app/index.html': No such file or directory
user@[컴퓨터명]:~$ docker images mission-web
                                                                                                                                                                            i Info →   U  In UseIMAGE            ID             DISK USAGE   CONTENT SIZE   EXTRA
mission-web:v1   3b4795625258       92.7MB         26.1MB    U
user@[컴퓨터명]:~$ mkdir -p ~/mission-step6/app
user@[컴퓨터명]:~$ cp ~/mission-step5/app/index.html ~/mission-step6/app/index.html
user@[컴퓨터명]:~$ cd ~/mission-step6
user@[컴퓨터명]:~/mission-step6$ pwd
/home/user/mission-step6
user@[컴퓨터명]:~/mission-step6$ cat app/index.html
<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>My Docker Web Server</h1>
    <p>Mission 5: Nginx custom image</p>
    <p>This page was built with Docker.</p>
</body>
</html>
user@[컴퓨터명]:~/mission-step6$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED             STATUS             PORTS                                     NAMES
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   About an hour ago   Up About an hour   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   3 hours ago         Up 3 hours                                                   mission4-ubuntu
user@[컴퓨터명]:~/mission-step6$ docker inspect --format '{{json .Mounts}}' mission-web-bind

error: no such object: mission-web-bind
user@[컴퓨터명]:~/mission-step6$ docker run -d --name mission-web-bind -p 8081:80 --mount type=bind,source=/home/user/mission-step6/app,target=/usr/share/nginx/html,readonly mission-web:v1
9ad4aaf662ba4518ba57dade2ed7e63246966f08ed1782b56f211cea89a92215
user@[컴퓨터명]:~/mission-step6$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED             STATUS             PORTS                                     NAMES
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   36 seconds ago      Up 35 seconds      0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   About an hour ago   Up About an hour   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   3 hours ago         Up 3 hours                                                   mission4-ubuntu
user@[컴퓨터명]:~/mission-step6$ docker inspect --format '{{json .Mounts}}' mission-web-bind
[{"Type":"bind","Source":"/home/user/mission-step6/app","Destination":"/usr/share/nginx/html","Mode":"","RW":false,"Propagation":"rprivate"}]
user@[컴퓨터명]:~/mission-step6$ curl -i http://localhost:8081
HTTP/1.1 200 OK
Server: nginx/1.30.6
Date: Sat, 07 Nov 2026 02:51:24 GMT
Content-Type: text/html
Content-Length: 257
Last-Modified: Sat, 07 Nov 2026 02:45:53 GMT
Connection: keep-alive
ETag: "6aee90e1-101"
Accept-Ranges: bytes

<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>My Docker Web Server</h1>
    <p>Mission 5: Nginx custom image</p>
    <p>This page was built with Docker.</p>
</body>
</html>
user@[컴퓨터명]:~/mission-step6$ nano ~/mission-step6/app/index.html
user@[컴퓨터명]:~/mission-step6$ cat ~/mission-step6/app/index.html
<h1>My Docker Web Server - Updated</h1><h1>My Docker Web Server - Updated</h1><h1>My Docker Web Server - Updated</h1><!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>My Docker Web Server</h1>
    <p>Mission 5: Nginx custom image</p>
    <p>This page was built with Docker.</p>
</body>
</html>
user@[컴퓨터명]:~/mission-step6$ cat > ~/mission-step6/app/index.html <<'EOF'
> <!doctype html>
> <html lang="ko">
> <head>
>     <meta charset="utf-8">
>     <title>My Docker Web Server</title>
> </head>
> <body>
>     <h1>My Docker Web Server - Updated</h1>
>     <p>Mission 6: Bind mount update successful</p>
>     <p>This page was built with Docker.</p>
> </body>
> </html>
> EOF
user@[컴퓨터명]:~/mission-step6$ cat ~/mission-step6/app/index.html
<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>My Docker Web Server - Updated</h1>
    <p>Mission 6: Bind mount update successful</p>
    <p>This page was built with Docker.</p>
</body>
</html>
user@[컴퓨터명]:~/mission-step6$ curl -i http://localhost:8081
HTTP/1.1 200 OK
Server: nginx/1.30.6
Date: Sat, 07 Nov 2026 03:03:35 GMT
Content-Type: text/html
Content-Length: 277
Last-Modified: Sat, 07 Nov 2026 03:01:46 GMT
Connection: keep-alive
ETag: "6aee949a-115"
Accept-Ranges: bytes

<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>My Docker Web Server - Updated</h1>
    <p>Mission 6: Bind mount update successful</p>
    <p>This page was built with Docker.</p>
</body>
</html>
user@[컴퓨터명]:~/mission-step6$ docker exec mission-web-bind cat /usr/share/nginx/html/index.html
<!doctype html>
<html lang="ko">
<head>
    <meta charset="utf-8">
    <title>My Docker Web Server</title>
</head>
<body>
    <h1>My Docker Web Server - Updated</h1>
    <p>Mission 6: Bind mount update successful</p>
    <p>This page was built with Docker.</p>
</body>
</html>
user@[컴퓨터명]:~/mission-step6$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                     NAMES
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   20 minutes ago   Up 20 minutes   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago      Up 2 hours      0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago      Up 3 hours                                                mission4-ubuntu
user@[컴퓨터명]:~/mission-step6$
```
