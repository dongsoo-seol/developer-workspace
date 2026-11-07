# 5단계 전체 실행 기록 — 공개용 사본

학습자가 제공한 첨부 텍스트의 순서를 보존했다. 명령을 다시 실행해 만든 로그가 아니다.

- 원본: 붙여넣은 텍스트.txt
- 편집: 줄바꿈을 LF로 정규화하고 개인 컴퓨터명·엔진 식별자·내부 프록시 및 레지스트리 주소를 마스킹했다.
- 컨테이너 ID, 이미지 digest, HTTP 응답 등 검증에 필요한 값은 유지했다.
- 빌드 성공 출력은 있지만 해당 빌드의 명령 입력 줄은 원본에 없다. 보고서의 빌드 명령은 재현용으로 구분한다.

원본 바이트의 SHA-256:

```text
594b0c5e6d0730f33ad96fd0c4dcc697fc7a0ffb8ec8386be59412a7c2b42143
```

## 실제 실행 기록

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
 Containers: 9
  Running: 1
  Paused: 0
  Stopped: 8
 Images: 3
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

user@[컴퓨터명]:/mnt/c/Users/user$ cd ~
user@[컴퓨터명]:~$ mkdir mission-step5
user@[컴퓨터명]:~$ cd mission-step5
user@[컴퓨터명]:~/mission-step5$ pwd
/home/user/mission-step5
user@[컴퓨터명]:~/mission-step5$ mkdir app
user@[컴퓨터명]:~/mission-step5$ docker pull nginx:stable-alpine
stable-alpine: Pulling from library/nginx
4fd2a979a136: Pull complete
163ee6b178c7: Pull complete
881fc932939e: Pull complete
52eb9c26a936: Pull complete
89fcb8c38289: Pull complete
c12ffff18732: Pull complete
0e13a8a92b02: Pull complete
d04b4ed66a45: Pull complete
77ecefd98398: Download complete
99a9f441c054: Download complete
Digest: sha256:583b7f2fce1cdb8da5ad5c39ebd3c33b98b87d493190e548bdee1fa8c619c311
Status: Downloaded newer image for nginx:stable-alpine
docker.io/library/nginx:stable-alpine
user@[컴퓨터명]:~/mission-step5$ docker image inspect --format '{{index .RepoDigests 0}}' nginx:stable-alpine
nginx@sha256:583b7f2fce1cdb8da5ad5c39ebd3c33b98b87d493190e548bdee1fa8c619c311
user@[컴퓨터명]:~/mission-step5$ nano app/index.html
user@[컴퓨터명]:~/mission-step5$ cat app/index.html
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
user@[컴퓨터명]:~/mission-step5$ nano Dockerfile
user@[컴퓨터명]:~/mission-step5$ cat Dockerfile
FROM nginx:stable-alpine
COPY app/ /usr/share/nginx/html/
user@[컴퓨터명]:~/mission-step5$ nano .dockerignore
user@[컴퓨터명]:~/mission-step5$ ls -la
total 20
drwxr-xr-x  3 user user 4096 Nov  7 10:30 .
drwxr-x--- 12 user user 4096 Nov  7 10:23 ..
-rw-r--r--  1 user user   26 Nov  7 10:30 .dockerignore
-rw-r--r--  1 user user   58 Nov  7 10:28 Dockerfile
drwxr-xr-x  2 user user 4096 Nov  7 10:27 app
[+] Building 3.5s (7/7) FINISHED                                                                                                                                                 docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                       0.1s
 => => transferring dockerfile: 95B                                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/nginx:stable-alpine                                                                                                                     0.1s
 => [internal] load .dockerignore                                                                                                                                                          0.1s
 => => transferring context: 66B                                                                                                                                                           0.0s
 => [1/2] FROM docker.io/library/nginx:stable-alpine@sha256:583b7f2fce1cdb8da5ad5c39ebd3c33b98b87d493190e548bdee1fa8c619c311                                                               2.0s
 => => resolve docker.io/library/nginx:stable-alpine@sha256:583b7f2fce1cdb8da5ad5c39ebd3c33b98b87d493190e548bdee1fa8c619c311                                                               0.1s
 => => extracting sha256:881fc932939efef04ca120ad44bbd2c0917851eb247aa4995428afb1e80ede17                                                                                                  0.4s
 => => extracting sha256:52eb9c26a93672fd16d692befd22944ee26a59772beff8c775b37c684b0da36f                                                                                                  0.3s
 => => extracting sha256:89fcb8c382892a31f2b913ee3e11af133e228ec75e84351e4f848d6f83c0b56e                                                                                                  0.0s
 => => extracting sha256:163ee6b178c7d7aa60ef7115424399a0ccb8f293e43f6b35cd6df8f152698c6a                                                                                                  0.0s
 => => extracting sha256:0e13a8a92b02d6d710028ebada164686467e25ddff8907ae28a80bafc26bbee4                                                                                                  0.0s
 => => extracting sha256:c12ffff18732349750d8928e649e2333d19363a2a201978b1876c80f38aaf292                                                                                                  0.0s
 => => extracting sha256:d04b4ed66a450f2c2675e1c80127ba32721eeda72aaac8709d6ff707ccf90070                                                                                                  0.0s
 => => extracting sha256:4fd2a979a136065b9518cac19c8f3d11f2251813831dc2d18a4edb1d1b26374d                                                                                                  0.7s
 => [internal] load build context                                                                                                                                                          0.1s
 => => transferring context: 327B                                                                                                                                                          0.0s
 => [2/2] COPY app/ /usr/share/nginx/html/                                                                                                                                                 0.4s
 => exporting to image                                                                                                                                                                     0.5s
 => => exporting layers                                                                                                                                                                    0.2s
 => => exporting manifest sha256:5d2255545b0a40b163fa39932d1f99082c322f843d6c1efa2217c9f9fd61d0c3                                                                                          0.0s
 => => exporting config sha256:8bf9b3d7a696491b391abf7710538c312f02adacb594687d0d1d68aaaba8af1a                                                                                            0.0s
 => => exporting attestation manifest sha256:e25e7c37d0b2f6bfaee150f08e11ea69507b4655d7d49f529a515482bcf093a0                                                                              0.1s
 => => exporting manifest list sha256:3b4795625258d7683b196d464cfa5a6b866bb85fe1284cafb11b186a8aaab4b7                                                                                     0.0s
 => => naming to docker.io/library/mission-web:v1                                                                                                                                          0.0s
 => => unpacking to docker.io/library/mission-web:v1                                                                                                                                       0.0s
user@[컴퓨터명]:~/mission-step5$ docker images mission-web
                                                                                                                                                                            i Info →   U  In UseIMAGE            ID             DISK USAGE   CONTENT SIZE   EXTRA
mission-web:v1   3b4795625258       92.7MB         26.1MB
user@[컴퓨터명]:~/mission-step5$ docker run -d --name mission-web -p 8080:80 mission-web:v1
28712a7251bb41dafea19baee9f09cad975f51a0eacf9e81ed0370ab715e808f
user@[컴퓨터명]:~/mission-step5$ docker ps
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS          PORTS                                     NAMES
28712a7251bb   mission-web:v1   "/docker-entrypoint.…"   45 seconds ago   Up 41 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   mission-web
75ae287f9aaa   ubuntu:24.04     "bash"                   2 hours ago      Up 2 hours                                                mission4-ubuntu
user@[컴퓨터명]:~/mission-step5$ docker logs mission-web
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/11/07 01:33:48 [notice] 1#1: using the "epoll" event method
2026/11/07 01:33:48 [notice] 1#1: nginx/1.30.6
2026/11/07 01:33:48 [notice] 1#1: built by gcc 15.2.0 (Alpine 15.2.0)
2026/11/07 01:33:48 [notice] 1#1: OS: Linux 6.18.54.1-microsoft-standard-WSL2
2026/11/07 01:33:48 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/11/07 01:33:48 [notice] 1#1: start worker processes
2026/11/07 01:33:48 [notice] 1#1: start worker process 30
2026/11/07 01:33:48 [notice] 1#1: start worker process 31
2026/11/07 01:33:48 [notice] 1#1: start worker process 32
2026/11/07 01:33:48 [notice] 1#1: start worker process 33
user@[컴퓨터명]:~/mission-step5$
user@[컴퓨터명]:~/mission-step5$ curl -i http://localhost:8080
HTTP/1.1 200 OK
Server: nginx/1.30.6
Date: Sat, 07 Nov 2026 01:50:43 GMT
Content-Type: text/html
Content-Length: 257
Last-Modified: Sat, 07 Nov 2026 01:27:14 GMT
Connection: keep-alive
ETag: "6aee7e72-101"
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
user@[컴퓨터명]:~/mission-step5$ cat .dockerignore
.git
docs
*.log
README.md
user@[컴퓨터명]:~/mission-step5$
```
