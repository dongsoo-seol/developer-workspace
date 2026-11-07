# 4단계. Docker 기본 운영 및 컨테이너 수명 관찰

## 1. 실습 개요와 기록 기준

Docker 이미지 조회·가져오기, 컨테이너 실행·종료·재시작, 로그·리소스 조회를 수행했다. 주 프로세스 종료, exec 추가 셸 종료, attach 연결 분리를 비교하여 컨테이너 수명을 관찰했다.

이 보고서는 학습자가 로컬 Windows PC의 WSL2 Ubuntu에서 실행해 제공한 실제 터미널 기록에 근거한다. 명령과 출력은 분리했으며 긴 목록·로그는 필요한 부분만 발췌하고 표시했다. 안내용 예시를 실행 결과로 사용하지 않았다. 절의 배치는 요구사항별이다. 실제로는 최초 이미지 다운로드·hello-world 실행 후 컨테이너 수명 실습을 했고, 마지막에 이미지와 hello-world를 다시 확인했다.

[시간 순서대로 보존한 전체 실행 기록 — 공개용 사본](evidence/04-docker-session.md)

| 항목 | 내용 |
|---|---|
| 실습일 | 2026-11-07, KST |
| 작업 환경 | Windows 10 Pro / WSL2 Ubuntu 26.04.1 LTS / Bash |
| Docker | 1단계에서 CLI·엔진 29.9.2, Docker Desktop 확인 |
| Docker 명령 실행 위치 | WSL Ubuntu, 당시 현재 위치는 `/mnt/c/Users/user` |
| 사용 이미지 | `ubuntu:24.04`, `hello-world:latest` |
| 실습 컨테이너 | `mission4-ubuntu` |
| 컨테이너 ID | `75ae287f9aaa` |
| 주 프로세스 | 생성 명령에서 지정한 `bash` |

Docker 명령을 실행한 WSL Ubuntu와 Docker가 만든 Ubuntu 컨테이너는 서로 다른 환경이다. 이번 컨테이너 내부에서 Docker 명령을 찾을 수 없었던 오류도 실제 기록에 포함했다.

<a id="verification"></a>

## 2. 요구사항별 수행 결과와 증거 위치

| 완료 | 요구사항 | 확인 방법·결과 | 증거 |
|---|---|---|---|
| [x] | 이미지 가져오기 | 최초 다운로드 성공, 후반에는 같은 이미지의 최신 상태 확인 | [이미지 확인](#images) |
| [x] | 이미지 목록 | ubuntu:24.04와 hello-world:latest 확인 | [이미지 목록](#images) |
| [x] | hello-world 실행 | Hello from Docker! 출력 | [실행 결과](#hello-world) |
| [x] | Ubuntu 실행·내부 명령 | bash 진입, ls /와 echo 출력 | [내부 실행](#main-shell) |
| [x] | 실행 중·전체 목록 | docker ps 및 docker ps -a로 상태 비교 | [주 셸 종료](#main-exit), [재시작](#restart) |
| [x] | 주 셸 종료 관찰 | exit 후 Exited (127), 로그로 원인 확인 | [종료·원인](#main-exit) |
| [x] | exec 종료 관찰 | 추가 셸에서 exit 후에도 Up | [exec](#exec) |
| [x] | attach 분리 관찰 | Ctrl+P, Ctrl+Q로 분리 후에도 Up | [attach](#attach) |
| [x] | 로그 조회 | 실행 중인 컨테이너의 실제 로그 확인 | [로그](#logs) |
| [x] | 리소스 조회 | stats에서 CPU·메모리 등 확인 | [리소스](#stats) |
| [x] | 명시적 중지·재시작 | stop 후 Exited (137), start 후 Up | [중지·재시작](#stop-start) |

<a id="images"></a>

## 3. 이미지 가져오기와 목록 확인

### 3-1. 최초 이미지 다운로드

**실행 명령**

```bash
docker pull ubuntu:24.04
```

**실제 출력**

```text
24.04: Pulling from library/ubuntu
7a48b8f0e5c7: Pull complete
98144013299c: Download complete
Digest: sha256:fe81560990fae86dc4743202b2cb4d3df98d0bcf949173cecef15ce1127ef852
Status: Downloaded newer image for ubuntu:24.04
docker.io/library/ubuntu:24.04
```

**해석:** 이미지 가져오기가 완료됐고, 명시적으로 Downloaded newer image 응답을 확인했다. 다운로드 요구사항의 직접 증거이다.

### 3-2. 실습 후반의 재확인

**실행 명령**

```bash
docker pull ubuntu:24.04
```

**실제 출력**

```text
24.04: Pulling from library/ubuntu
Digest: sha256:fe81560990fae86dc4743202b2cb4d3df98d0bcf949173cecef15ce1127ef852
Status: Image is up to date for ubuntu:24.04
docker.io/library/ubuntu:24.04
```

**해석:** 후반 재확인에서는 최초와 동일한 digest에 대해 최신 상태라는 응답을 받았다. 최초 다운로드 성공과 이 후반의 재사용 결과는 별개의 실행으로 구분한다.

### 3-3. 이미지 목록

**실행 명령**

```bash
docker images
```

**실제 출력 — 표시 간격 정리**

```text
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   c567b231f656       25.9kB         9.49kB    U
ubuntu:24.04         fe81560990fa        119MB         31.7MB    U
ubuntu:latest        6a06111c3b0a        163MB         45.9MB    U
```

ubuntu:24.04가 목록에 존재함을 확인했다. 이 환경의 CLI는 IMAGE, DISK USAGE, CONTENT SIZE 등의 열을 사용하는 형식으로 출력했다. 교재와 열 이름이 달라도 이미지·태그를 기준으로 확인할 수 있다.

<a id="hello-world"></a>

## 4. hello-world 실행

**실행 명령**

```bash
docker run --rm hello-world
```

**제공된 실제 출력의 핵심 부분 발췌**

```text
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
```

**해석:** 실제 성공 문구를 확인했다. 위 메시지는 이미지에 포함된 일반 안내 문구이며, 이번 실행에서 캐시된 이미지를 반드시 다시 다운로드했다는 네트워크 증거는 아니다. 앞선 이미지 목록에는 이미 hello-world가 있었다.

전체 실행 기록에는 컨테이너 실습 전과 후에 각각 hello-world를 실행한 두 번의 성공 출력이 있다. 위 발췌는 두 실행에서 공통으로 확인된 메시지이며, [전체 기록](evidence/04-docker-session.md)에서 각 실행 위치를 확인할 수 있다.

명령의 --rm은 컨테이너 종료 시 자동 제거하는 옵션이다. 따라서 이 실행의 컨테이너를 나중에 목록에서 찾는 방식 대신, 이번 터미널의 실제 성공 출력을 증거로 사용한다. 이전부터 남아 있던 hello-world 컨테이너의 종료 상태를 이번 실행의 증거로 대신 사용하지 않았다.

<a id="main-shell"></a>

## 5. Ubuntu 컨테이너 실행과 내부 명령

**WSL Ubuntu에서 실행**

```bash
docker run -it --name mission4-ubuntu ubuntu:24.04 bash
```

**실제 진입 프롬프트**

```text
root@75ae287f9aaa:/#
```

프롬프트는 입력할 명령이 아니라, 컨테이너 내부에 진입했음을 보여주는 표시이다.

| 옵션·인수 | 역할 |
|---|---|
| run | 새 컨테이너 생성·실행 |
| -it | 표준입력 유지 및 대화형 터미널 할당 |
| --name | 컨테이너 이름 지정 |
| ubuntu:24.04 | 사용할 이미지·태그 |
| bash | 컨테이너의 주 프로세스로 실행 |

**컨테이너 내부에서 실행한 명령**

```bash
ls /
```

**실제 출력 — 이후 docker logs에서도 확인**

```text
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

echo 입력 과정에서는 한글이 터미널 기록에 8진 이스케이프 형태로 표시됐고, 닫는 따옴표 뒤에 exit가 붙은 입력이 있었다. 이를 정상적인 별도 exit 실행으로 고쳐 적지 않고, 실제로 출력된 문구를 남긴다.

**실제 echo 결과**

```text
주 프로세스 셸에서 실행했습니다.exit
```

echo와 exit가 별개 명령이어야 하는데 글자로 함께 출력됐던 상황이다. 이후 exit를 별도 줄로 실행했다. 상세 원인은 [오류 기록](#troubleshooting)에 설명한다.

<a id="main-exit"></a>

## 6. 주 셸 종료와 컨테이너 중지 관찰

컨테이너 내부에서 Docker 목록을 조회하려다 다음 오류가 발생했다. 원본 로그에서 확인한 기록이다.

```text
root@75ae287f9aaa:/# docker ps
bash: docker: command not found
```

그 후 컨테이너 내부에서 별도 명령으로 종료했다.

```bash
exit
```

**실제 출력**

```text
exit
```

WSL Ubuntu로 돌아온 뒤 전체 목록을 조회했다.

```bash
docker ps -a
```

**실제 목록 중 실습 컨테이너 행 발췌**

```text
CONTAINER ID   IMAGE          COMMAND    CREATED         STATUS                        PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"     6 minutes ago   Exited (127) 26 seconds ago             mission4-ubuntu
```

**해석:** 주 프로세스인 Bash가 종료되자 컨테이너가 중지됐다. 종료 코드 127은 직전의 명령 미인식 결과와 연결된다. Bash의 exit에 값을 지정하지 않으면 직전 명령 상태가 전달될 수 있으므로, 기록을 Exited (0)으로 바꾸지 않았다.

<a id="restart"></a>

## 7. 기존 컨테이너 재시작

**WSL Ubuntu에서 실행**

```bash
docker start mission4-ubuntu
```

**실제 출력**

```text
mission4-ubuntu
```

**상태 확인**

```bash
docker ps
```

**실제 출력**

```text
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS          PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    10 minutes ago   Up 15 seconds             mission4-ubuntu
```

동일한 이름·ID의 컨테이너가 다시 실행됐다. 새로 생성하는 run과 기존 컨테이너를 다시 시작하는 start의 차이를 확인했다.

<a id="exec"></a>

## 8. exec로 추가 셸을 열고 종료

**WSL Ubuntu에서 실행**

```bash
docker exec -it mission4-ubuntu bash
```

**추가 셸 내부에서 실행**

```bash
echo "exec shell test"
```

**실제 출력**

```text
exec shell test
```

**추가 셸 종료**

```bash
exit
```

**실제 출력**

```text
exit
```

**WSL Ubuntu로 돌아와 확인**

```bash
docker ps
```

**실제 출력**

```text
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS         PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    13 minutes ago   Up 2 minutes             mission4-ubuntu
```

**해석:** 추가 Bash만 종료됐고 원래 주 프로세스는 계속 실행되어 컨테이너가 Up 상태를 유지했다.

<a id="attach"></a>

## 9. attach로 주 셸 연결 후 분리

**WSL Ubuntu에서 실행**

```bash
docker attach mission4-ubuntu
```

**연결된 주 셸에서 실행**

```bash
echo "attach shell test"
```

**실제 출력**

```text
attach shell test
```

분리 키 Ctrl+P, Ctrl+Q를 순서대로 사용했다. 키 조작은 일반 명령처럼 입력하는 문자열이 아니다. 제공된 터미널에는 분리 시 다음 안내가 표시됐다.

```text
read escape sequence
```

**WSL Ubuntu로 돌아온 뒤 실행**

```bash
docker ps
```

**실제 출력**

```text
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS         PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    20 minutes ago   Up 9 minutes             mission4-ubuntu
```

**해석:** 터미널 연결만 분리됐고 컨테이너는 실행을 유지했다. attach는 새로운 Bash를 만드는 것이 아니라 기존 주 프로세스의 입출력에 연결하는 동작이다.

<a id="logs"></a>

## 10. 로그 확인

**실행 중인 컨테이너에 대해 WSL Ubuntu에서 실행**

```bash
docker logs mission4-ubuntu
```

**실제 로그 중 확인에 필요한 부분 발췌**

중간의 한글 이스케이프 입력 줄과 일부 프롬프트는 생략했다. 아래는 실제 출력된 내용을 발췌한 것이다.

```text
root@75ae287f9aaa:/# ls /
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
주 프로세스 셸에서 실행했습니다.exit
root@75ae287f9aaa:/# docker ps
bash: docker: command not found
root@75ae287f9aaa:/# exit
exit
root@75ae287f9aaa:/# echo "attach shell test"
attach shell test
root@75ae287f9aaa:/# ls /
```

로그의 command not found는 과거에 발생한 오류 기록이며, logs 조회가 그 명령을 다시 실행한 것은 아니다. 끝의 ls 입력 뒤 결과는 제공된 기록에 나타나지 않아 추가 목록이 출력됐다고 주장하지 않는다.

docker logs는 일반적으로 컨테이너 주 프로세스의 표준출력·표준에러 기록을 확인한다. exec로 실행한 추가 프로세스의 출력이 같은 로그에 반드시 포함되는 것은 아니다. exec shell test는 8절의 실제 exec 세션 기록에서 확인했다.

<a id="stats"></a>

## 11. 리소스 사용량 확인

**실행 명령**

```bash
docker stats --no-stream mission4-ubuntu
```

**실제 출력**

```text
CONTAINER ID   NAME              CPU %     MEM USAGE / LIMIT     MEM %     NET I/O         BLOCK I/O        PIDS
75ae287f9aaa   mission4-ubuntu   0.00%     1.039MiB / 3.786GiB   0.03%     1.17kB / 126B   3.13MB / 4.1kB   1
```

| 항목 | 실제 값 | 해석 |
|---|---|---|
| CPU | 0.00% | 조회 순간 대기 중인 컨테이너의 낮은 사용률 |
| 메모리 | 1.039MiB / 3.786GiB | 조회 순간 사용량 / 제한 |
| 메모리 비율 | 0.03% | 제한 대비 사용률 |
| 네트워크 | 1.17kB / 126B | 수신·송신 누적량 |
| 블록 I/O | 3.13MB / 4.1kB | 블록 장치 읽기·쓰기 누적량 |
| PIDS | 1 | 조회 순간 표시된 프로세스·스레드 수 항목 |

no-stream 옵션을 사용해 연속 갱신 대신 한 번의 표본을 확인했다. 이 값은 해당 시점의 상태이며 항상 동일한 값이 나와야 하는 것은 아니다.

<a id="stop-start"></a>

## 12. 명시적 중지와 재시작

**실행 명령**

```bash
docker stop mission4-ubuntu
```

**실제 출력**

```text
mission4-ubuntu
```

**전체 목록 확인**

```bash
docker ps -a
```

**실제 목록 중 해당 행 발췌**

```text
CONTAINER ID   IMAGE          COMMAND    CREATED          STATUS                       PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"     24 minutes ago   Exited (137) 8 seconds ago             mission4-ubuntu
```

137은 보통 128+9, 즉 SIGKILL에 의한 종료와 관련된다. docker stop은 먼저 종료 신호를 보내고 제한 시간 내에 종료되지 않으면 강제로 종료할 수 있다. 다만 이 목록만으로 정확한 신호 처리 경로나 메모리 부족 여부까지 확정하지 않는다. 확인된 사실은 stop 후 Exited (137)이었고 이후 재시작이 성공했다는 것이다.

**재시작 명령**

```bash
docker start mission4-ubuntu
```

**실제 출력**

```text
mission4-ubuntu
```

**실행 중 목록 확인**

```bash
docker ps
```

**실제 출력**

```text
CONTAINER ID   IMAGE          COMMAND   CREATED          STATUS          PORTS     NAMES
75ae287f9aaa   ubuntu:24.04   "bash"    25 minutes ago   Up 12 seconds             mission4-ubuntu
```

재시작 전후 컨테이너 ID가 같아 기존 컨테이너를 다시 사용한 것을 확인했다.

<a id="comparison"></a>

## 13. 컨테이너 수명 비교

| 실제 수행 | 확인된 상태 | 이유·해석 |
|---|---|---|
| 주 Bash에서 exit | Exited (127) | 주 프로세스 종료. 코드는 직전 명령 미인식 결과와 연결 |
| start 후 exec 추가 셸에서 exit | Up 2 minutes | 추가 셸만 종료되고 주 Bash는 유지 |
| attach 후 Ctrl+P, Ctrl+Q | Up 9 minutes | 기존 주 프로세스에 대한 연결만 분리 |
| docker stop | Exited (137) | 명시적 중지 완료. 강제 종료 관련 코드 기록 |
| docker start | Up 12 seconds | 기존 컨테이너 재시작 |

주 프로세스가 실행 중인지와 터미널이 연결되어 있는지는 다른 조건이다. 터미널 연결을 분리해도 주 프로세스가 살아 있으면 컨테이너는 실행을 유지한다.

| 구분 | 역할 |
|---|---|
| 이미지 | 컨테이너 생성의 기반이 되는 파일시스템·실행 설정 |
| 컨테이너 | 이미지로 만든 개별 실행 환경과 상태 |
| run | 새 컨테이너 생성·실행 |
| start | 기존 컨테이너 시작 |
| exec | 실행 중인 컨테이너에 새 프로세스 실행 |
| attach | 기존 주 프로세스의 입출력에 연결 |

<a id="troubleshooting"></a>

## 14. 트러블슈팅

### 14-1. echo 뒤에 exit가 붙음

- **문제:** 문장 뒤에 exit가 글자로 출력되고 셸이 종료되지 않았다.
- **원인 가설:** 명령을 줄 단위로 구분하지 않고 echo 입력 뒤에 exit를 붙였다.
- **확인:** 실제 출력에 문장 끝의 exit가 포함됐고 컨테이너 프롬프트가 유지됐다.
- **해결:** exit를 별도 줄에 입력했다.
- **후속 확인:** WSL 프롬프트로 돌아왔고 ps -a에서 컨테이너 중지를 확인했다.
- **배운 점:** 한 명령을 입력한 뒤 Enter를 눌러 실행하고 다음 명령을 입력한다.

### 14-2. 컨테이너 안에서 Docker 명령 실행

- **문제:** 컨테이너 내부의 docker ps가 command not found로 실패했다.
- **원인 가설:** 컨테이너와 Docker를 조작하는 WSL 환경을 혼동했다.
- **확인:** logs에 컨테이너 프롬프트와 명령 미인식 오류가 함께 남았다.
- **해결:** WSL Ubuntu로 돌아와 Docker 명령을 실행했다.
- **재검증:** 같은 컨테이너에 start와 ps를 실행해 Up 15 seconds를 확인했다.
- **배운 점:** 이번 환경의 Ubuntu 컨테이너에는 Docker CLI가 없으며, Docker 조작은 WSL Ubuntu에서 수행한다. 컨테이너 안의 Ubuntu와 호스트 쪽 Ubuntu는 별도 환경이다.

### 14-3. attach 분리 키 조작 혼동

- **문제:** 분리 시도 중 컨테이너 입력 줄에 ls /p가 표시됐고 주 셸에 남아 있었다.
- **원인 가설:** 키 조합 대신 일반 입력이 들어갔거나 입력 줄이 남은 상태였다.
- **확인:** 해당 시점에 컨테이너 프롬프트가 유지됐다. ls /p의 실행 결과는 제공되지 않아 실행 여부는 확정하지 않는다.
- **안내된 조치:** Ctrl+U로 입력 줄을 비우고 Ctrl+P, Ctrl+Q를 순서대로 사용한다.
- **후속 확인:** 실제로 WSL 프롬프트로 돌아왔고 ps에서 Up 9 minutes를 확인했다.
- **배운 점:** 분리 키는 입력할 명령 문자열이 아닌 터미널 키 조작이다.

## 15. 완료 판정과 정리 방법

필수 이미지·컨테이너 운영 명령, hello-world 성공, Ubuntu 내부 명령, 주 셸·추가 셸·연결 분리 비교를 모두 실제 기록으로 확인했다. 4단계 필수 실습은 완료했다.

수집된 마지막 mission4-ubuntu 상태 조회는 Up 12 seconds였다. 그 뒤 별도로 중지했다는 추가 기록은 없다. 실습 후 정리할 때는 아래 명령으로 자신이 만든 컨테이너를 중지할 수 있다. 아래는 정리 방법 안내이며 추가 실행 결과가 아니다.

```bash
docker stop mission4-ubuntu
```

이번 실습에서는 이전부터 있던 다른 컨테이너를 삭제하지 않았다. 보고서 정리 후에도 이미지·컨테이너를 일괄 삭제하는 명령은 필요하지 않다.

웹 서버 빌드, 포트 매핑, 마운트·볼륨, GitHub·VS Code 연동은 다음 단계의 별도 검증 대상이다.
