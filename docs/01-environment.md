# 1단계. 실행 환경 확인 및 도구 점검

## 1. 목표와 기록 기준

Windows에서 WSL2 Ubuntu를 실행하고, 기본 쉘과 Git·Docker의 설치 상태를 확인한다. Docker 클라이언트의 버전 확인과 Docker 엔진의 실제 응답 확인을 구분한다.

아래 결과는 학습자가 자신의 Windows PC에서 실행해 제공한 기록이다. 개인 컴퓨터명이 포함된 프롬프트는 제거했다. 긴 출력은 발췌 사실을 표시했으며, 클라우드 환경에서 수행한 별도의 테스트 결과를 로컬 PC의 결과로 포함하지 않았다.

## 2. 실행 환경 표

| 항목 | 실제 확인 결과 | 확인 근거 |
|---|---|---|
| Windows | Microsoft Windows 10 Pro, 10.0.19045 | PowerShell의 OS 조회 결과 |
| 사용한 작업 창 | Windows PowerShell | 사용자가 확인한 창 제목 |
| Linux 실행 기반 | WSL2 | WSL 목록의 Ubuntu VERSION 2 |
| Linux 배포판 | Ubuntu 26.04.1 LTS | `/etc/os-release` |
| Ubuntu 기본 쉘 | Bash, `/bin/bash` | `echo "$SHELL"` |
| Windows Git | 2.56.0.windows.4 | PowerShell의 `git --version` |
| Docker CLI | 29.9.2, build a82e528 | Ubuntu의 `docker --version` |
| Docker 엔진 | 29.9.2 | Ubuntu의 `docker info` Server 항목 |
| Docker 실행 환경 | Docker Desktop, Linux 컨테이너 | Server의 Operating System·OSType |

Git 버전은 Windows에서 확인한 값이다. 이 결과만으로 Ubuntu 안의 Git 설치 상태까지 확인한 것은 아니다.

<a id="windows"></a>

## 3. Windows 확인

**실행 위치:** Windows PowerShell

```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version
```

**학습자가 제공한 확인 결과**

```text
Microsoft Windows 10 Pro 10.0.19045
```

<a id="wsl"></a>

## 4. WSL과 Ubuntu 확인

**실행 위치:** Windows PowerShell

```powershell
wsl --list --verbose
```

**당시 실제 출력**

```text
  NAME              STATE           VERSION
* Ubuntu            Stopped         2
  docker-desktop    Stopped         2
```

Ubuntu의 VERSION이 2이므로 WSL2로 설치된 것을 확인했다. `Stopped`는 조회 당시 실행 중이 아니었다는 뜻이다. 이후 Ubuntu 실행 및 Docker 점검 결과와 모순되지 않는다.

Ubuntu 진입에 사용한 명령은 다음과 같다. 같은 명령의 실행은 이후 3단계 시작 로그에서도 확인된다.

```powershell
wsl -d Ubuntu
```

**실행 위치:** Ubuntu

```bash
cat /etc/os-release
```

**실제 출력 중 버전 정보 발췌**

```text
PRETTY_NAME="Ubuntu 26.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="26.04"
VERSION="26.04.1 LTS (Resolute Raccoon)"
VERSION_CODENAME=resolute
ID=ubuntu
ID_LIKE=debian
```

## 5. 기본 쉘 확인

**실행 위치:** Ubuntu

```bash
echo "$SHELL"
```

**실제 출력**

```text
/bin/bash
```

기본 쉘이 Bash로 설정된 것을 확인했다. 쉘은 입력한 명령을 해석하고 실행하는 프로그램이다.

<a id="git"></a>

## 6. Git 설치와 작동 확인

처음 PowerShell에서 버전을 조회했을 때 Git 명령을 찾지 못했다.

```powershell
git --version
```

**실제 오류의 핵심 부분 발췌**

```text
git : 'git' 용어가 cmdlet, 함수, 스크립트 파일 또는 실행할 수 있는 프로그램 이름으로 인식되지 않습니다.
```

설치에 안내된 명령은 다음과 같다. 학습자가 이어서 제공한 설치 로그로 설치 성공을 확인했다.

```powershell
winget install --id Git.Git -e --source winget
```

**제공된 설치 로그 중 핵심 부분 발췌**

```text
찾음 Git [Git.Git] 버전 2.56.0.4
설치 관리자 해시를 확인했습니다.
패키지 설치를 시작하는 중...
설치 성공
```

설치 후 PowerShell에서 재확인했다.

```powershell
git --version
```

**실제 출력**

```text
git version 2.56.0.windows.4
```

설치 성공 메시지뿐 아니라 실제 명령 실행으로 Git 사용 가능 여부를 검증했다. 최초 오류가 미설치 때문인지 PATH 반영 문제 때문인지는 최초 출력만으로 확정하지 않았다. 상세 과정은 [트러블슈팅 1](troubleshooting.md#issue-01)에 정리했다.

<a id="docker"></a>

## 7. Docker 버전과 데몬 점검

**실행 위치:** Ubuntu

```bash
docker --version
```

**실제 출력**

```text
Docker version 29.9.2, build a82e528
```

이 명령은 클라이언트 버전 확인이다. 엔진과 연결되는지도 확인하기 위해 다음 명령을 실행했다.

```bash
docker info
```

**실제 출력 중 점검에 필요한 부분 발췌**

아래 출력은 긴 원본에서 선택한 부분이다. 플러그인 목록, 장치 식별자, 내부 프록시 설정 등은 생략했다.

```text
Client:
 Version:    29.9.2
 Context:    default
 Debug Mode: false

Server:
 Containers: 8
  Running: 0
  Paused: 0
  Stopped: 8
 Images: 2
 Server Version: 29.9.2
 Storage Driver: overlayfs
 Logging Driver: local
 Cgroup Driver: cgroupfs
 Cgroup Version: 2
 Kernel Version: 6.18.54.1-microsoft-standard-WSL2
 Operating System: Docker Desktop
 OSType: linux
 Architecture: x86_64
 CPUs: 4
 Total Memory: 3.786GiB
```

**판정:** Client뿐 아니라 Server 정보가 정상 출력되어 Docker 엔진과 통신 가능한 상태임을 확인했다. `Running: 0`은 당시 실행 중인 컨테이너가 없었다는 뜻이다.

`Containers: 8`, `Images: 2`는 조회 시점의 상태 정보이다. 이 수치를 컨테이너 생성·중지 실습의 증거로 대신 사용하지 않는다. 로컬 PC의 `hello-world`, Ubuntu 컨테이너 내부 실행 및 Docker 운영 명령 실습은 이후 단계에서 별도로 증명해야 한다.

## 8. 도구별 역할

| 도구 | 이번 환경에서의 역할 |
|---|---|
| Windows | 일상 작업을 수행하는 호스트 운영체제 |
| PowerShell | Windows 명령 실행과 WSL 진입 |
| WSL2 | Windows에서 Linux 환경을 실행하는 기반 |
| Ubuntu | Linux 파일·경로·권한 실습을 수행하는 운영체제 |
| Bash | Ubuntu에서 명령을 해석하고 실행 |
| Docker Desktop | Docker 엔진과 컨테이너 실행 환경 제공 |
| Git | 파일 변경 이력을 로컬 저장소에서 관리 |

## 9. 완료 판정

- [x] Windows와 Ubuntu 버전 확인
- [x] WSL2와 기본 쉘 확인
- [x] Git 설치 후 실제 버전 조회 성공
- [x] Docker CLI 버전 확인
- [x] Docker 엔진 Server 응답 확인
- [x] 환경 표·명령·실제 출력·판정 근거 문서화

1단계의 환경 점검은 완료했다. Git 사용자 정보·기본 브랜치 설정, GitHub 인증·푸시, VS Code 연동은 별도 요구사항이며 현재 기록에는 해당 검증 결과가 없다.
