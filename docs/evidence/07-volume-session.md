# 7단계 전체 실행 기록 — 공개용 사본

학습자가 첨부한 전체 텍스트의 실행 순서를 보존했다. 컴퓨터명을 마스킹하고 줄바꿈을 LF로 정규화했다. 오류와 반복 실행도 원래 순서로 유지했다.

- 원본: 붙여넣은 텍스트.txt
- 첫 검증: 2d37feccfaf1 삭제 → a62ed3789c49 생성·동일 데이터 읽기
- 후속 검증: 9a7353841aec 삭제 → 83b287f14feb 생성·읽기 전용으로 동일 데이터 읽기
- 두 회차는 같은 mission-data 볼륨을 사용했다. 같은 컨테이너 이름의 서로 다른 ID를 구분한다.

원본 바이트의 SHA-256:

```text
611e6d902f22f2641b82b52cc951e2cfe242a8af24d1553630c4f1b959a7c885
```

## 실제 실행 기록

```text
PS C:\Users\user> wsl -d Ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume ls
DRIVER    VOLUME NAME
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS                    PORTS                                     NAMES
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   40 minutes ago   Up 40 minutes             0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago      Up 2 hours                0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago      Up 3 hours                                                          mission4-ubuntu
9464f39b44cc   ubuntu           "bash"                   2 days ago       Exited (129) 2 days ago                                             mission1-ubuntu
6332a18f60cc   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               charming_rubin
41cf1df1c148   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               blissful_hofstadter
86997a6cd49f   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               bold_banzai
0782527e408c   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               strange_mayer
abe4a0c49198   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               elated_faraday
eb43b4fbd3a9   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               elated_easley
004ddf4c8a0f   hello-world      "/hello"                 3 days ago       Exited (0) 3 days ago                                               objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume create mission-data
mission-data
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume inspect mission-data
[
    {
        "CreatedAt": "2026-11-07T03:30:48Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mission-data/_data",
        "Name": "mission-data",
        "Options": null,
        "Scope": "local"
    }
]
user@[컴퓨터명]:/mnt/c/Users/user$ docker run -dit --name mission7-first --mount type=volume,source=mission-data,target=/data ubuntu:24.04 bash
2d37feccfaf15900dec9bc42f769733eefa072b1330b590260edcf81a6cae6c4
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                     NAMES
2d37feccfaf1   ubuntu:24.04     "bash"                   21 seconds ago   Up 20 seconds                                             mission7-first
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   42 minutes ago   Up 42 minutes   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago      Up 2 hours      0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago      Up 3 hours                                                mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker inspect --format '{{json .Mounts}}' mission7-first
[{"Type":"volume","Name":"mission-data","Source":"/var/lib/docker/volumes/mission-data/_data","Destination":"/data","Driver":"local","Mode":"z","RW":true,"Propagation":""}]
user@[컴퓨터명]:/mnt/c/Users/user$ docker exec mission7-first sh -c 'echo "Mission 7 persistent data" > /data/record.txt'
user@[컴퓨터명]:/mnt/c/Users/user$ docker exec mission7-first cat /data/record.txt
Mission 7 persistent data
user@[컴퓨터명]:/mnt/c/Users/user$ docker stop mission7-first
mission7-first
user@[컴퓨터명]:/mnt/c/Users/user$ docker rm mission7-first
mission7-first
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS                    PORTS                                     NAMES
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   44 minutes ago   Up 44 minutes             0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago      Up 2 hours                0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago      Up 3 hours                                                          mission4-ubuntu
9464f39b44cc   ubuntu           "bash"                   2 days ago       Exited (129) 2 days ago                                             mission1-ubuntu
6332a18f60cc   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               charming_rubin
41cf1df1c148   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               blissful_hofstadter
86997a6cd49f   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               bold_banzai
0782527e408c   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               strange_mayer
abe4a0c49198   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               elated_faraday
eb43b4fbd3a9   hello-world      "/hello"                 2 days ago       Exited (0) 2 days ago                                               elated_easley
004ddf4c8a0f   hello-world      "/hello"                 3 days ago       Exited (0) 3 days ago                                               objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume ls
DRIVER    VOLUME NAME
local     mission-data
user@[컴퓨터명]:/mnt/c/Users/user$ docker run -dit --name mission7-second --mount type=volume,source=mission-data,target=/data ubuntu:24.04 bash
a62ed3789c49a8d1eb019dba2e58c0f89e3a43634df913286602bf9f0fc9c8ee
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                     NAMES
a62ed3789c49   ubuntu:24.04     "bash"                   27 seconds ago   Up 26 seconds                                             mission7-second
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   46 minutes ago   Up 46 minutes   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago      Up 2 hours      0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago      Up 4 hours                                                mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker exec mission7-second cat /data/record.txt
Mission 7 persistent data
user@[컴퓨터명]:/mnt/c/Users/user$ docs/07-volume-persistence.md
-bash: docs/07-volume-persistence.md: No such file or directory
user@[컴퓨터명]:/mnt/c/Users/user$ docker stop mission7-second
mission7-second
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume create mission-data
mission-data
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume inspect mission-data
[
    {
        "CreatedAt": "2026-11-07T03:30:48Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mission-data/_data",
        "Name": "mission-data",
        "Options": null,
        "Scope": "local"
    }
]
user@[컴퓨터명]:/mnt/c/Users/user$ docker run -dit --name mission7-first --mount type=volume,source=mission-data,target=/data ubuntu:24.04 bash
9a7353841aec4859ee3e6055845a76f63ae5ec3468a63125e3c94e5589507c34
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                     NAMES
9a7353841aec   ubuntu:24.04     "bash"                   10 seconds ago   Up 9 seconds                                              mission7-first
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   52 minutes ago   Up 52 minutes   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago      Up 2 hours      0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago      Up 4 hours                                                mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker inspect --format '{{json .Mounts}}' mission7-first
[{"Type":"volume","Name":"mission-data","Source":"/var/lib/docker/volumes/mission-data/_data","Destination":"/data","Driver":"local","Mode":"z","RW":true,"Propagation":""}]
user@[컴퓨터명]:/mnt/c/Users/user$ docker exec mission7-first sh -c 'echo "Mission 7 persistent data" > /data/record.txt'
user@[컴퓨터명]:/mnt/c/Users/user$ docker exec mission7-first cat /data/record.txt
Mission 7 persistent data
user@[컴퓨터명]:/mnt/c/Users/user$ docker stop mission7-first
mission7-first
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED             STATUS                        PORTS                                     NAMES
9a7353841aec   ubuntu:24.04     "bash"                   8 minutes ago       Exited (137) 8 seconds ago                                              mission7-first
a62ed3789c49   ubuntu:24.04     "bash"                   14 minutes ago      Exited (137) 12 minutes ago                                             mission7-second
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   About an hour ago   Up About an hour              0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago         Up 2 hours                    0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago         Up 4 hours                                                              mission4-ubuntu
9464f39b44cc   ubuntu           "bash"                   2 days ago          Exited (129) 2 days ago                                                 mission1-ubuntu
6332a18f60cc   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   charming_rubin
41cf1df1c148   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   blissful_hofstadter
86997a6cd49f   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   bold_banzai
0782527e408c   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   strange_mayer
abe4a0c49198   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   elated_faraday
eb43b4fbd3a9   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   elated_easley
004ddf4c8a0f   hello-world      "/hello"                 3 days ago          Exited (0) 3 days ago                                                   objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume ls
DRIVER    VOLUME NAME
local     mission-data
user@[컴퓨터명]:/mnt/c/Users/user$ docker rm mission7-first
mission7-first
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED             STATUS                        PORTS                                     NAMES
a62ed3789c49   ubuntu:24.04     "bash"                   17 minutes ago      Exited (137) 15 minutes ago                                             mission7-second
9ad4aaf662ba   mission-web:v1   "/docker-entrypoint.…"   About an hour ago   Up About an hour              0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   mission-web-bind
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   2 hours ago         Up 2 hours                    0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   4 hours ago         Up 4 hours                                                              mission4-ubuntu
9464f39b44cc   ubuntu           "bash"                   2 days ago          Exited (129) 2 days ago                                                 mission1-ubuntu
6332a18f60cc   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   charming_rubin
41cf1df1c148   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   blissful_hofstadter
86997a6cd49f   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   bold_banzai
0782527e408c   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   strange_mayer
abe4a0c49198   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   elated_faraday
eb43b4fbd3a9   hello-world      "/hello"                 2 days ago          Exited (0) 2 days ago                                                   elated_easley
004ddf4c8a0f   hello-world      "/hello"                 3 days ago          Exited (0) 3 days ago                                                   objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ docker volume ls
DRIVER    VOLUME NAME
local     mission-data
user@[컴퓨터명]:/mnt/c/Users/user$ docker run -dit --name mission7-verify --mount type=volume,source=mission-data,target=/data,readonly ubuntu:24.04 bash
83b287f14febbef3051f48e72c9c7faa8d9c7a0ec7c1006b9046750785ca6c79
user@[컴퓨터명]:/mnt/c/Users/user$ docker inspect --format '{{json .Mounts}}' mission7-verify
[{"Type":"volume","Name":"mission-data","Source":"/var/lib/docker/volumes/mission-data/_data","Destination":"/data","Driver":"local","Mode":"z","RW":false,"Propagation":""}]
user@[컴퓨터명]:/mnt/c/Users/user$ docker exec mission7-verify cat /data/record.txt
Mission 7 persistent data
user@[컴퓨터명]:/mnt/c/Users/user$
```
