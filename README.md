# 개발 워크스테이션 구축 — 1~7단계 수행 보고서

> 정리일: 2026-11-07, KST. 환경 점검·터미널·권한·Docker 운영·웹 서버·바인드 마운트·볼륨 영속성 결과를 정리했다. 1~7단계 기능 실습은 확인됐고 8단계는 아직 완료되지 않았다. 브라우저 화면은 대화에서 확인했으며 원본 PNG의 제출 폴더 배치는 남아 있다.

## 1. 프로젝트 개요

터미널, Docker, Git/GitHub를 활용하여 재현 가능한 개발 환경을 구축하는 미션이다. 환경 확인에서 시작하여 파일·경로·권한을 익히고, 이후 웹 서버 컨테이너 제작과 네트워크·스토리지 연결, GitHub 공유까지 확장하는 것을 목표로 한다.

Windows에서 WSL2 Ubuntu를 사용해 Linux 기본 조작과 권한 변경을 수행했다. Docker는 버전·엔진 점검에 이어 이미지 확인, hello-world, Ubuntu 컨테이너 운영, 주 셸·exec·attach의 수명 비교까지 확인했다.

공식 Nginx 베이스에 직접 작성한 HTML을 추가한 mission-web:v1 이미지도 제작했다. 컨테이너 기동 로그, HTTP 200 응답과 본문, 브라우저 화면으로 웹 서버의 실제 동작을 확인했다.

## 2. 실행 환경

| 항목 | 실제 확인 결과 |
|---|---|
| 호스트 OS | Microsoft Windows 10 Pro, 10.0.19045 |
| 사용한 터미널·작업 창 | Windows PowerShell |
| Linux 환경 | WSL2 / Ubuntu 26.04.1 LTS |
| Ubuntu 기본 쉘 | Bash, `/bin/bash` |
| Windows Git | 2.56.0.windows.4 |
| Docker CLI | 29.9.2, build a82e528 |
| Docker 엔진 | 29.9.2, Docker Desktop / Linux 컨테이너 |
| Docker 데몬 점검 | Ubuntu의 docker info에서 Server 정보 수신 성공 |
| 웹 서버 베이스 | nginx:stable-alpine, 실제 Nginx 버전 1.30.6 |
| 커스텀 웹 서버 | mission-web:v1 / 컨테이너 mission-web / 포트 8080→80 |
| 2단계 실습 위치 | `/home/user/mission-step2` |
| 3단계 실습 위치 | `/home/user/mission-step3` |

[실행 환경 확인 명령과 실제 출력](docs/01-environment.md)

Git 버전은 Windows에서 확인한 결과이며, Ubuntu 안의 Git 설치 여부까지 증명하는 것은 아니다.

## 3. 전체 수행 체크리스트

- [x] 1단계: 실행 환경 및 Git·Docker 기본 점검
- [x] 2단계: 터미널 조작 및 절대·상대 경로 실습
- [x] 3단계: 파일·디렉터리 권한 변경 및 해석
- [x] 4단계: Docker 이미지·컨테이너 기본 운영, hello-world, Ubuntu 내부 실행
- [x] 5단계: Dockerfile 기반 커스텀 웹 서버 이미지 제작
- [x] 6단계: 포트 접속 및 바인드 마운트 변경 반영
- [x] 7단계: Docker 볼륨 데이터 영속성 검증
- [ ] 8단계: Git 설정·GitHub·VS Code 연동 및 최종 제출

웹 서버 접속, 바인드 마운트 변경 반영, 컨테이너 삭제 후 볼륨 데이터 유지까지 확인했다. GitHub 연동·업로드 완료는 아직 확인되지 않았다.

## 4. 현재까지의 핵심 결과

### 4-1. 환경 점검

Git 명령 미인식 문제 이후 설치를 진행하고 버전 조회에 성공했다. Docker는 버전 조회와 별도로 Server 응답까지 확인했다.

**실제 확인 명령 — Windows PowerShell**

```powershell
git --version
```

**실제 출력**

```text
git version 2.56.0.windows.4
```

**실제 확인 명령 — Ubuntu**

```bash
docker --version
docker info
```

**버전 출력**

```text
Docker version 29.9.2, build a82e528
```

**docker info의 핵심 부분 발췌**

```text
Server Version: 29.9.2
Operating System: Docker Desktop
OSType: linux
```

[환경·Git·Docker 상세 검증 기록](docs/01-environment.md)

### 4-2. 터미널 및 경로 실습

전용 디렉터리에서 파일 생성·읽기·복사·이동·이름 변경·삭제를 수행하고 목록·내용으로 검증했다. 같은 파일을 서로 다른 위치에서 읽어 다음 차이를 확인했다.

| 현재 위치 | 같은 파일을 가리키는 상대 경로 | 절대 경로 |
|---|---|---|
| `/home/user/mission-step2` | `./notes.txt` | `/home/user/mission-step2/notes.txt` |
| `/home/user` | `mission-step2/notes.txt` | `/home/user/mission-step2/notes.txt` |

두 위치에서 동일한 두 줄을 확인했다. 상대 경로는 현재 위치에 따라 달라지지만 같은 파일의 절대 경로는 유지됐다.

[터미널 조작과 경로 비교 전체 기록](docs/02-terminal-paths.md)

### 4-3. 파일·디렉터리 권한 실습

초기 권한이 최종 목표와 같았으므로, 먼저 다른 권한으로 바꾼 뒤 되돌려 실제 변경 과정을 기록했다.

| 대상 | 최초 | 제한 후 | 파일만 재변경한 중간 상태 | 최종 |
|---|---|---|---|---|
| 파일 | 644 | 600 | 644 | 644 |
| 디렉터리 | 755 | 700 | 700 | 755 |

**최종 확인 명령**

```bash
ls -ld permission-file.txt permission-dir
```

**실제 출력**

```text
drwxr-xr-x 2 user user 4096 Nov  7 07:58 permission-dir
-rw-r--r-- 1 user user    0 Nov  7 07:57 permission-file.txt
```

[권한 변경 전후 전체 기록과 해석](docs/03-permissions.md)

### 4-4. Docker 운영과 컨테이너 수명 관찰

ubuntu:24.04를 최초로 가져올 때 Downloaded newer image 응답을 확인했고, 실습 후반에는 동일 digest에 대해 최신 상태라는 응답을 확인했다. hello-world는 실습 전후 각각 실행하여 성공 문구를 확인했다. mission4-ubuntu를 만들고 내부 명령, 실행·중지·재시작, 로그·리소스 조회를 수행했다.

| 수행 | 실제 확인 결과 |
|---|---|
| 주 Bash에서 exit | Exited (127). 직전 Docker 명령 미인식 로그로 원인 확인 |
| exec 추가 셸에서 exit | 주 프로세스는 유지되어 Up 2 minutes |
| attach 연결 후 분리 | 연결만 분리되어 Up 9 minutes |
| 명시적 stop 후 | Exited (137). 정확한 신호 처리 경로는 목록만으로 확정하지 않음 |
| 다시 start 후 | 동일 컨테이너가 Up 12 seconds |

리소스 조회 순간 CPU 0.00%, 메모리 1.039MiB / 3.786GiB를 확인했다.

[4단계 전체 기록](docs/04-docker-operations.md), [수명 비교표](docs/04-docker-operations.md#comparison)

[첨부 텍스트의 시간 순서별 실행 증거 — 컴퓨터명·내부 정보 마스킹](docs/evidence/04-docker-session.md)

### 4-5. 커스텀 Nginx 이미지와 웹 서버

공식 베이스 nginx:stable-alpine에서 Nginx 실행 환경을 활용하고, app/index.html을 웹 루트에 복사했다. 빌드 완료와 이미지 ID 3b4795625258을 확인했다. mission-web 컨테이너의 호스트 8080번 포트를 컨테이너 80번에 연결했다.

| 검증 | 실제 결과 |
|---|---|
| 빌드 | BuildKit FINISHED, mission-web:v1 생성 |
| 실행 | 컨테이너 28712a7251bb, Up 41 seconds |
| 로그 | nginx/1.30.6, worker 시작 |
| 기능 요청 | HTTP/1.1 200 OK, 직접 작성한 HTML 본문 |
| 브라우저 | localhost:8080 주소창과 My Docker Web Server 확인 |

[5단계 보고서](docs/05-custom-image.md), [전체 실행 증거](docs/evidence/05-web-session.md)

소스: [Dockerfile](Dockerfile), [HTML](app/index.html), [.dockerignore](.dockerignore)

브라우저 PNG는 현재 묶음에 포함되지 않았다. [이미지 추가 안내](docs/images/이미지추가안내.txt)에 따라 저장한 파일을 넣으면 문서에 표시할 수 있다.

### 4-6. 포트 매핑과 바인드 마운트 검증

기존 5단계 컨테이너는 8080번을 사용하므로, 새 mission-web-bind를 8081번에 실행했다. 호스트의 /home/user/mission-step6/app을 웹 루트에 읽기 전용으로 연결하고, 호스트에서 HTML을 수정했다.

| 항목 | 수정 전 | 수정 후 |
|---|---|---|
| 주소 | localhost:8081 | localhost:8081 |
| 컨테이너 ID | 9ad4aaf662ba | 9ad4aaf662ba |
| 상태 | Up 35 seconds | Up 20 minutes |
| HTTP | 200 OK | 200 OK |
| Content-Length | 257 | 277 |
| 본문 | My Docker Web Server / Mission 5 | My Docker Web Server - Updated / Mission 6 |

제공된 명령 흐름에서 재빌드·재시작 없이 웹 응답이 바뀌었다. 컨테이너 내부 파일과 브라우저 화면에도 같은 내용이 반영됐다.

[6단계 보고서](docs/06-port-bind-mount.md), [전체 실행 증거](docs/evidence/06-bind-session.md), [수정 후 HTML](examples/step6/app/index.html)

수정 전후 PNG는 대화에서 확인했지만 압축본에는 포함되지 않았다. docs/images/06-bind-before.png와 06-bind-after.png를 저장한 뒤 보고서에 표시해야 한다.

### 4-7. 이름 있는 볼륨 데이터 영속성

mission-data 볼륨에 파일을 저장한 첫 컨테이너를 삭제하고, 새 검증 컨테이너에 같은 볼륨을 읽기 전용으로 연결했다. 파일을 다시 작성하지 않고 이전 문장을 읽는 데 성공했다.

| 항목 | 첫 컨테이너 | 삭제 후 새 검증 컨테이너 |
|---|---|---|
| 이름 | mission7-first | mission7-verify |
| ID | 9a7353841aec | 83b287f14feb |
| 볼륨 | mission-data | mission-data |
| 연결 경로 | /data | /data |
| RW | true | false |
| 파일 내용 | Mission 7 persistent data | Mission 7 persistent data |

첫 컨테이너는 stop만 한 것으로 끝내지 않고 rm과 목록의 부재로 삭제를 확인했다. 삭제 후에도 볼륨이 남아 있음을 조회한 다음 새 컨테이너를 만들었다.

[7단계 상세 보고서](docs/07-volume-persistence.md), [삭제 증거](docs/07-volume-persistence.md#delete), [재연결·데이터 비교](docs/07-volume-persistence.md#comparison)

[7단계 전체 실행 기록](docs/evidence/07-volume-session.md)에는 두 회차가 있다. 최초 작성 컨테이너 2d37feccfaf1을 삭제한 뒤 a62ed3789c49에서 읽기에 성공했고, 후속 작성 컨테이너 9a7353841aec를 삭제한 뒤 83b287f14feb에서 읽기 전용 검증에도 성공했다. 위 표는 후속 회차를 기준으로 한다.

## 5. 요구사항별 검증 방법 및 증거 위치

| 요구사항 | 검증 방법·판정 기준 | 실제 결과 위치 |
|---|---|---|
| OS·WSL·쉘 | OS 조회, WSL VERSION, 쉘 환경변수 확인 | [1단계](docs/01-environment.md) |
| Git 설치 | 설치 로그 이후 실제 버전 조회 성공 | [Git 점검](docs/01-environment.md#git) |
| Docker 설치·데몬 | 버전 확인과 Server 응답을 별도 확인 | [Docker 점검](docs/01-environment.md#docker) |
| 현재 위치·숨김 목록 | 현재 경로와 점으로 시작하는 항목 확인 | [목록 조회](docs/02-terminal-paths.md#log-01) |
| 디렉터리 생성·이동 | 생성 후 목록·현재 경로 확인 | [디렉터리 실습](docs/02-terminal-paths.md#log-02) |
| 빈 파일·내용 작성 | 크기 0 및 저장한 두 줄 확인 | [빈 파일](docs/02-terminal-paths.md#log-03), [내용 작성](docs/02-terminal-paths.md#log-05) |
| 복사·이동·이름 변경 | 원본·복사본·목적지의 목록과 내용 비교 | [복사](docs/02-terminal-paths.md#log-06), [이동](docs/02-terminal-paths.md#log-07), [이름 변경](docs/02-terminal-paths.md#log-08) |
| 삭제 | 삭제 전 존재와 삭제 후 부재 비교 | [삭제](docs/02-terminal-paths.md#log-09) |
| 절대·상대 경로 | 같은 파일의 내용을 위치별로 비교 | [경로 비교](docs/02-terminal-paths.md#log-10), [위치 변경](docs/02-terminal-paths.md#log-11) |
| 파일·디렉터리 권한 | 초기·중간·최종 문자 권한 비교 | [권한 비교표](docs/03-permissions.md#comparison) |
| 숫자 권한 해석 | r=4, w=2, x=1을 세 부류별로 계산 | [권한 해석](docs/03-permissions.md#concepts) |
| 이미지·hello-world | 최초 다운로드·후반 재사용·목록·실제 성공 문구 | [이미지](docs/04-docker-operations.md#images), [hello-world](docs/04-docker-operations.md#hello-world) |
| Ubuntu 내부 실행 | 대화형 Bash에서 ls·echo 수행 | [내부 명령](docs/04-docker-operations.md#main-shell) |
| 주 셸·exec·attach | 종료·분리 후 컨테이너 상태 비교 | [수명 비교](docs/04-docker-operations.md#comparison) |
| Docker 로그·리소스 | 실행 중 컨테이너에 logs·stats 수행 | [로그](docs/04-docker-operations.md#logs), [리소스](docs/04-docker-operations.md#stats) |
| Docker 중지·재시작 | stop·start 후 Exited·Up 확인 | [운영 기록](docs/04-docker-operations.md#stop-start) |
| 커스텀 베이스·소스 | 공식 출처·digest·HTML·Dockerfile 확인 | [베이스](docs/05-custom-image.md#base), [소스](docs/05-custom-image.md#source) |
| 웹 서버 빌드·실행 | 빌드 완료·이미지·컨테이너·로그 확인 | [빌드](docs/05-custom-image.md#build), [실행](docs/05-custom-image.md#run) |
| 웹 응답·브라우저 | HTTP 200과 맞춤 HTML, 주소창 포함 화면 | [HTTP](docs/05-custom-image.md#http), [화면 상태](docs/05-custom-image.md#browser) |
| 바인드 연결 | inspect의 bind·Source·Destination·RW 확인 | [마운트 정보](docs/06-port-bind-mount.md#mount) |
| 변경 반영 | 호스트·컨테이너 파일, HTTP 본문, 화면 비교 | [수정 전후](docs/06-port-bind-mount.md#comparison) |
| 재생성 없이 유지 | 같은 ID, 이어지는 Up 시간, 실제 명령 흐름 | [컨테이너 확인](docs/06-port-bind-mount.md#identity) |
| 볼륨 생성·저장 | 이름·연결 경로·파일 작성 및 내용 조회 | [볼륨](docs/07-volume-persistence.md#volume), [삭제 전 데이터](docs/07-volume-persistence.md#write) |
| 삭제 후 데이터 유지 | 첫 컨테이너 삭제, 볼륨 존속, 새 ID에서 같은 내용 조회 | [삭제](docs/07-volume-persistence.md#delete), [새 컨테이너](docs/07-volume-persistence.md#verify), [읽기](docs/07-volume-persistence.md#read) |
| 트러블슈팅 2건 이상 | 문제→가설→확인→해결/대안→재검증 | [트러블슈팅](docs/troubleshooting.md) |

## 6. 트러블슈팅 요약

| 문제 | 확인 및 해결 결과 | 상세 |
|---|---|---|
| PowerShell에서 Git 명령 미인식 | 설치 후 버전 출력 성공. 미설치와 PATH 중 최초 원인은 확정하지 않음 | [사례 1](docs/troubleshooting.md#issue-01) |
| 디렉터리 경로를 명령처럼 입력 | 디렉터리임을 확인하고 올바른 위치에서 후속 파일 작업 성공 | [사례 2](docs/troubleshooting.md#issue-02) |
| Windows에서 Linux 클라우드 경로로 이동 실패 | 환경별 경로가 다름을 확인. 이후 로컬 WSL 홈에서 실습했으나 같은 Windows 경로 재시도 성공 기록은 없음 | [추가 사례 3](docs/troubleshooting.md#issue-03) |
| 컨테이너 내부의 Docker 명령 미인식 | 로그로 원인 확인, WSL에서 다시 start·ps 성공 | [4단계 오류 분석](docs/04-docker-operations.md#troubleshooting) |

## 7. 학습한 핵심 개념

- Ubuntu는 Linux 기반 운영체제이며, 현재 PC에서는 WSL2로 실행한다.
- 파일은 내용을 담고, 디렉터리는 파일과 하위 디렉터리를 정리한다.
- 절대 경로는 최상위 디렉터리에서, 상대 경로는 현재 위치에서 시작한다.
- 파일과 디렉터리는 같은 r/w/x 표기를 사용하지만 각 권한의 의미가 다르다.
- 스크립트는 명령을 순서대로 적어 실행하는 파일이다. 이번 기록에는 스크립트 작성·실행 실습은 포함되지 않는다.
- 이름 있는 볼륨은 컨테이너와 별도로 데이터를 유지한다. 이번에는 컨테이너 삭제 후 재연결로 확인했으며, 이를 별도 복사본을 만드는 백업과 구분했다.

[사례로 정리한 개념 복습 노트](docs/learning-notes.md)

## 8. 문서 구성

```text
README.md
Dockerfile
.dockerignore
app/
└── index.html
examples/
└── step6/
    └── app/
        └── index.html
제출안내.txt
docs/
├── 01-environment.md
├── 02-terminal-paths.md
├── 03-permissions.md
├── 04-docker-operations.md
├── 05-custom-image.md
├── 06-port-bind-mount.md
├── 07-volume-persistence.md
├── evidence/
│   ├── 04-docker-session.md
│   ├── 05-web-session.md
│   ├── 06-bind-session.md
│   └── 07-volume-session.md
├── images/
│   └── 이미지추가안내.txt
├── troubleshooting.md
├── learning-notes.md
└── submission-checklist.md
```

실습용 파일의 원본은 로컬 Ubuntu의 홈 디렉터리에 있다. 이 묶음의 5단계 HTML·Dockerfile·.dockerignore는 실제 cat 출력 내용으로 저장했다. 전체 로컬 파일시스템을 복사한 백업은 아니다.

## 9. 보안 및 기록 원칙

- 토큰·비밀번호·개인키·인증 코드를 포함하지 않았다.
- 개인 컴퓨터명, 내부 주소·식별자, 실습에 불필요한 설정 경로는 생략했다.
- 발췌 출력과 개념 설명용 예시를 실제 실행 로그와 구분했다.
- 실제로 수행하지 않은 컨테이너 실행·접속·인증 결과를 만들지 않았다.
- 다른 사용자 계정으로 접근 차단을 시험한 기록은 없으므로, 권한 실험은 소유자의 권한 변경과 표시 확인 범위로 보고했다.

## 10. 다음 단계와 최종 제출 상태

현재 1~7단계의 기능 실습과 보고서를 정리했다. 전체 과제의 최종 제출을 위해서는 아래 증거와 파일 정리가 추가로 필요하다.

- 이미 확인한 브라우저 접속 화면의 원본 PNG를 제출 폴더에 배치
- Git 사용자 정보·기본 브랜치 설정과 안전하게 정리한 설정 출력
- GitHub·VS Code 연동 및 푸시 결과, 저장소 접근 권한 확인

제출 대상 저장소: https://github.com/dongsoo-seol/developer-workspace

본 자료의 작성은 GitHub 업로드나 공개 설정 완료를 의미하지 않는다. 업로드 후 README와 상세 문서의 링크를 확인해야 한다. [현재 산출물 점검표와 전체 과제의 남은 항목](docs/submission-checklist.md)을 함께 사용한다.

## 11. 웹 서버 재현 방법 — 새 환경용

아래는 동일 소스가 있는 새 작업 환경에서 사용하는 재현용 명령이다. 기존에 mission-web이 실행 중이면 이름·포트가 이미 사용 중일 수 있으므로 이 명령을 그대로 반복할 필요는 없다.

Dockerfile과 app 폴더가 있는 저장소 루트에서 실행한다.

```bash
docker build -t mission-web:v1 .
docker run -d --name mission-web -p 8080:80 mission-web:v1
curl -i http://localhost:8080
```

확인 기준은 HTTP 200과 My Docker Web Server 문구이다. 원래 빌드는 stable-alpine 태그를 사용했으며 실제 digest는 5단계 보고서에 기록했다. 나중에 태그가 갱신될 수 있으므로 비트 단위로 동일한 베이스가 자동 보장되는 것은 아니다.
