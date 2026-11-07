# 4단계 전체 실행 기록 — 공개용 사본

학습자가 제공한 첨부 텍스트의 명령·출력 순서를 보존했다. 명령을 다시 실행해 만든 로그가 아니다.

- 원본 파일명: 붙여넣은 텍스트.txt
- 편집: CRLF를 LF로 정규화하고, 개인 컴퓨터명·Docker 엔진 식별자·내부 프록시 및 레지스트리 주소를 마스킹했다.
- 컨테이너 ID와 이미지 digest는 실습 대상 연결에 필요한 기술 증거로 유지했다.
- 아래에는 최초 다운로드와 후반의 최신 상태 재확인, hello-world 실행 2회의 기록이 포함된다.
- 과거 오류와 같은 로그의 반복 조회도 원래 순서대로 남겼다.

원본 바이트의 SHA-256(공개용 사본의 해시가 아님):

```text
727e7c2cbd05a731eb0f23288792dbcc2694fb512968a30eacbb2efc055a869e
```

## 실행 기록

아래는 입력 안내가 아닌 실제 수행 기록이다.

```text
PS C:\Users\user> wsl -d Ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker info

Client:
 Version:    29.9.2
 Context:    default
 Debug Mode: false
 Plugins:
  agent: Docker AI Agent Runner (Docker Inc.)
    Version:  v1.152.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-agent
  ai: Docker AI Agent - Ask Gordon (Docker Inc.)
    Version:  v1.33.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-ai
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.38.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.6.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-compose
  debug: Get a shell into any image or container (Docker Inc.)
    Version:  0.49.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-debug
  desktop: Docker Desktop commands (Docker Inc.)
    Version:  4.97.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-desktop
  dhi: CLI for managing Docker Hardened Images (Docker Inc.)
    Version:  v0.0.9
    Path:     /usr/local/lib/docker/cli-plugins/docker-dhi
  extension: Manages Docker extensions (Docker Inc.)
    Version:  v0.2.32
    Path:     /usr/local/lib/docker/cli-plugins/docker-extension
  init: Creates Docker-related starter files for your project (Docker Inc.)
    Version:  v1.4.1
    Path:     /usr/local/lib/docker/cli-plugins/docker-init
  mcp: Docker MCP Plugin (Docker Inc.)
    Version:  v0.44.2
    Path:     /usr/local/lib/docker/cli-plugins/docker-mcp
  model: Docker Model Runner (Docker Inc.)
    Version:  v1.2.9
    Path:     /usr/local/lib/docker/cli-plugins/docker-model
  offload: Docker Offload (Docker Inc.)
    Version:  v0.7.21
    Path:     /usr/local/lib/docker/cli-plugins/docker-offload
  pass: Docker Pass Secrets Manager Plugin (beta) (Docker Inc.)
    Version:  v0.3.2
    Path:     /usr/local/lib/docker/cli-plugins/docker-pass
  sandbox: "docker sandbox" is deprecated, use Docker Sandboxes instead (Docker Inc.)
    Version:  v0.13.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-sandbox
  scout: Docker Scout (Docker Inc.)
    Version:  v1.27.0
    Path:     /usr/local/lib/docker/cli-plugins/docker-scout

Server:
 Containers: 8
  Running: 0
  Paused: 0
  Stopped: 8
 Images: 2
 Server Version: 29.9.2
 Storage Driver: overlayfs
  driver-type: io.containerd.snapshotter.v1
 Logging Driver: local
 Cgroup Driver: cgroupfs
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Discovered Devices:
  cdi: docker.com/gpu=webgpu
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 nvidia runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: b7a25261e029d68978bbf290df0a62fc819b3c51
 runc version: v1.5.2-0-g29dd3dc2
 init version: de40ad0
 Security Options:
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.18.54.1-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
 CPUs: 4
 Total Memory: 3.786GiB
 Name: docker-desktop
 ID: [내부 식별자 생략]
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 HTTP Proxy: [내부 주소 생략]
 HTTPS Proxy: [내부 주소 생략]
 No Proxy: [내부 주소 생략]
 Labels:
  com.docker.desktop.address=unix:///var/run/docker-cli.sock
 Experimental: false
 Insecure Registries:
  [내부 레지스트리 주소 생략]
  127.0.0.0/8
  ::1/128
 Live Restore Enabled: false
 Firewall Backend: iptables

user@[컴퓨터명]:/mnt/c/Users/user$
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED      STATUS                    PORTS     NAMES
9464f39b44cc   ubuntu        "bash"     2 days ago   Exited (129) 2 days ago             mission1-ubuntu
6332a18f60cc   hello-world   "/hello"   2 days ago   Exited (0) 2 days ago               charming_rubin
41cf1df1c148   hello-world   "/hello"   2 days ago   Exited (0) 2 days ago               blissful_hofstadter
86997a6cd49f   hello-world   "/hello"   2 days ago   Exited (0) 2 days ago               bold_banzai
0782527e408c   hello-world   "/hello"   2 days ago   Exited (0) 2 days ago               strange_mayer
abe4a0c49198   hello-world   "/hello"   2 days ago   Exited (0) 2 days ago               elated_faraday
eb43b4fbd3a9   hello-world   "/hello"   2 days ago   Exited (0) 2 days ago               elated_easley
004ddf4c8a0f   hello-world   "/hello"   3 days ago   Exited (0) 3 days ago               objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ docker pull ubuntu:24.04
24.04: Pulling from library/ubuntu
7a48b8f0e5c7: Pull complete
98144013299c: Download complete
Digest: sha256:fe81560990fae86dc4743202b2cb4d3df98d0bcf949173cecef15ce1127ef852
Status: Downloaded newer image for ubuntu:24.04
docker.io/library/ubuntu:24.04
user@[컴퓨터명]:/mnt/c/Users/user$ docker images
                                                                                                    i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   c567b231f656       25.9kB         9.49kB    U
ubuntu:24.04         fe81560990fa        119MB         31.7MB
ubuntu:latest        6a06111c3b0a        163MB         45.9MB    U
user@[컴퓨터명]:/mnt/c/Users/user$ docker run --rm hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

user@[컴퓨터명]:/mnt/c/Users/user$ docker run -it --name mission4-ubuntu ubuntu:24.04 bash
root@75ae287f9aaa:/# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@75ae287f9aaa:/# echo "\354\243\274 \355\224\204\353\241\234\354\204\270
\354\212\244 \354\205\270\354\227\220\354\204\234 \354\213\2
44\355\226\211\355\226\210\354\212\265\353\213\210\353\213\2
44."exit
주 프로세스 셸에서 실행했습니다.exit
root@75ae287f9aaa:/# docker ps
bash: docker: command not found
root@75ae287f9aaa:/# exit
exit
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE          COMMAND    CREATED         STATUS                        PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"     6 minutes ago   Exited (127) 26 seconds ago             mission4-ubuntu
9464f39b44cc   ubuntu         "bash"     2 days ago      Exited (129) 2 days ago                 mission1-ubuntu
6332a18f60cc   hello-world    "/hello"   2 days ago      Exited (0) 2 days ago                   charming_rubin
41cf1df1c148   hello-world    "/hello"   2 days ago      Exited (0) 2 days ago                   blissful_hofstadter
86997a6cd49f   hello-world    "/hello"   2 days ago      Exited (0) 2 days ago                   bold_banzai
0782527e408c   hello-world    "/hello"   2 days ago      Exited (0) 2 days ago                   strange_mayer
abe4a0c49198   hello-world    "/hello"   2 days ago      Exited (0) 2 days ago                   elated_faraday
eb43b4fbd3a9   hello-world    "/hello"   2 days ago      Exited (0) 2 days ago                   elated_easley
004ddf4c8a0f   hello-world    "/hello"   3 days ago      Exited (0) 3 days ago                   objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ docker logs mission4-ubuntu
root@75ae287f9aaa:/# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@75ae287f9aaa:/# echo "\354\243\274 \355\224\204\353\241\234\354\204\270
\354\212\244 \354\205\270\354\227\220\354\204\234 \354\213\2
44\355\226\211\355\226\210\354\212\265\353\213\210\353\213\2
44."exit
주 프로세스 셸에서 실행했습니다.exit
root@75ae287f9aaa:/# docker ps
bash: docker: command not found
root@75ae287f9aaa:/# exit
exit
user@[컴퓨터명]:/mnt/c/Users/user$ docker start mission4-ubuntu
mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS          PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    10 minutes ago   Up 15 seconds             mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker exec -it mission4-ubuntu bash
root@75ae287f9aaa:/# echo "exec shell test"
exec shell test
root@75ae287f9aaa:/# exit
exit
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS         PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    13 minutes ago   Up 2 minutes             mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker attach mission4-ubuntu
root@75ae287f9aaa:/# echo "attach shell test"
attach shell test
root@75ae287f9aaa:/# read escape sequence
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS         PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    20 minutes ago   Up 9 minutes             mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker logs mission4-ubuntu
root@75ae287f9aaa:/# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@75ae287f9aaa:/# echo "\354\243\274 \355\224\204\353\241\234\354\204\270
\354\212\244 \354\205\270\354\227\220\354\204\234 \354\213\2
44\355\226\211\355\226\210\354\212\265\353\213\210\353\213\2
44."exit
주 프로세스 셸에서 실행했습니다.exit
root@75ae287f9aaa:/# docker ps
bash: docker: command not found
root@75ae287f9aaa:/# exit
exit
root@75ae287f9aaa:/# echo "attach shell test"
attach shell test
root@75ae287f9aaa:/# ls /


user@[컴퓨터명]:/mnt/c/Users/user$ docker stats --no-stream mission4-ubuntu
CONTAINER ID   NAME              CPU %     MEM USAGE / LIMIT     MEM %     NET I/O         BLOCK I/O        PIDS
75ae287f9aaa   mission4-ubuntu   0.00%     1.039MiB / 3.786GiB   0.03%     1.17kB / 126B   3.13MB / 4.1kB   1
user@[컴퓨터명]:/mnt/c/Users/user$ docker stop mission4-ubuntu
mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps -a
CONTAINER ID   IMAGE          COMMAND    CREATED          STATUS                       PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"     24 minutes ago   Exited (137) 8 seconds ago             mission4-ubuntu
9464f39b44cc   ubuntu         "bash"     2 days ago       Exited (129) 2 days ago                mission1-ubuntu
6332a18f60cc   hello-world    "/hello"   2 days ago       Exited (0) 2 days ago                  charming_rubin
41cf1df1c148   hello-world    "/hello"   2 days ago       Exited (0) 2 days ago                  blissful_hofstadter
86997a6cd49f   hello-world    "/hello"   2 days ago       Exited (0) 2 days ago                  bold_banzai
0782527e408c   hello-world    "/hello"   2 days ago       Exited (0) 2 days ago                  strange_mayer
abe4a0c49198   hello-world    "/hello"   2 days ago       Exited (0) 2 days ago                  elated_faraday
eb43b4fbd3a9   hello-world    "/hello"   2 days ago       Exited (0) 2 days ago                  elated_easley
004ddf4c8a0f   hello-world    "/hello"   3 days ago       Exited (0) 3 days ago                  objective_tu
user@[컴퓨터명]:/mnt/c/Users/user$ docker start mission4-ubuntu
mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker ps
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS          PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    25 minutes ago   Up 12 seconds             mission4-ubuntu
user@[컴퓨터명]:/mnt/c/Users/user$ docker pull ubuntu:24.04
24.04: Pulling from library/ubuntu
Digest: sha256:fe81560990fae86dc4743202b2cb4d3df98d0bcf949173cecef15ce1127ef852
Status: Image is up to date for ubuntu:24.04
docker.io/library/ubuntu:24.04
user@[컴퓨터명]:/mnt/c/Users/user$ docker images
                                                                                                    i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   c567b231f656       25.9kB         9.49kB    U
ubuntu:24.04         fe81560990fa        119MB         31.7MB    U
ubuntu:latest        6a06111c3b0a        163MB         45.9MB    U
user@[컴퓨터명]:/mnt/c/Users/user$ docker run --rm hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

user@[컴퓨터명]:/mnt/c/Users/user$
```
