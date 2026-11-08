# 평가 항목 16: 포트 충돌 진단·해제·재검증 실행 기록

> 수행일: 2026-11-08(KST). Windows PowerShell과 Docker Desktop에서 학습자가 실행하고 제공한 명령·출력에 근거한다. 별도의 가상 실행 결과를 만들지 않았다. 출력은 관련 부분만 발췌했으며, 아무 결과가 없었던 조회는 그 사실을 코드블록 밖에 기록했다.

## 1. 초기 상태와 8080 포트 점유 준비

```powershell
docker ps
Get-NetTCPConnection -LocalPort 8080 -State Listen -ErrorAction SilentlyContinue | Select-Object LocalAddress,LocalPort,OwningProcess
```

처음 docker ps에는 제목 행만 나왔고, Windows 조회에서도 항목이 없었다. 이어 docker ps -a에서 기존 mission-web(28712a7251bb)의 중지 상태와 8080 설정을 확인했다.

```powershell
docker start mission-web
```

```text
mission-web
```

```powershell
docker ps
```

해당 행에서 확인한 실제 값 발췌:

```text
CONTAINER ID: 28712a7251bb
IMAGE: mission-web:v1
STATUS: Up 13 seconds
PORTS: 0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
NAMES: mission-web
```

위는 표 전체의 원형이 아니라 관련 행의 값을 항목별로 발췌한 것이다.

## 2. 같은 포트 사용으로 충돌 재현

```powershell
docker run -d --name mission16-conflict -p 8080:80 mission-web:v1
```

생성된 컨테이너 ID:

```text
e0cfe5e5a7581bef24764df49d0d62d88a2677bf647e7e6159a6388729804e88
```

오류 메시지의 핵심 부분:

```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```

컨테이너 ID가 출력됐지만 네트워크 연결은 실패했다. 기존 mission-web이 사용 중인 호스트 8080을 새 컨테이너에도 지정한 것이 충돌 재현 조건이다.

## 3. Windows 점유 PID와 프로세스 이름 확인

```powershell
Get-NetTCPConnection -LocalPort 8080 -State Listen | Select-Object LocalAddress,LocalPort,OwningProcess
```

```text
LocalAddress LocalPort OwningProcess
------------ --------- -------------
::1               8080         14720
::                8080         12572
127.0.0.1         8080         14720
```

```powershell
Get-NetTCPConnection -LocalPort 8080 -State Listen | Select-Object -ExpandProperty OwningProcess -Unique | ForEach-Object { Get-Process -Id $_ } | Select-Object Id,ProcessName
```

```text
   Id ProcessName
   -- -----------
14720 wslrelay
12572 com.docker.backend
```

OwningProcess는 수신 중인 항목의 Windows PID이다. 해당 PID의 이름은 WSL·Docker Desktop 관련 중계 프로세스였고, Docker 목록에서는 mission-web의 8080 공개를 확인했다. 두 종류의 조회를 함께 사용해 컨테이너와 호스트의 점유를 진단했다. PID는 당시 값이므로 재실행 시 달라질 수 있다.

## 4. 점유 컨테이너 중지와 포트 해제 확인

Docker Desktop 기반 프로세스를 강제 종료하지 않고, 점유 대상으로 확인한 컨테이너만 중지했다.

```powershell
docker stop mission-web
```

```text
mission-web
```

```powershell
docker ps --filter publish=8080
```

```text
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

```powershell
Get-NetTCPConnection -LocalPort 8080 -State Listen -ErrorAction SilentlyContinue | Select-Object LocalAddress,LocalPort,OwningProcess
```

마지막 조회는 항목 출력 없이 다음 PowerShell 입력 줄로 돌아왔다. Docker의 실행 중 포트 공개 목록과 Windows의 Listen 조회 모두에서 8080 점유가 없어졌음을 확인했다.

## 5. 단순 재시작 후에도 실제 포트가 연결되지 않은 중간 결과

```powershell
docker start mission16-conflict
```

```text
mission16-conflict
```

```powershell
docker ps --filter name=mission16-conflict
```

관련 행의 실제 값 발췌:

```text
CONTAINER ID: e0cfe5e5a758
STATUS: Up 12 seconds
PORTS: 80/tcp
NAMES: mission16-conflict
```

```powershell
curl.exe -i http://localhost:8080
```

```text
curl: (7) Failed to connect to localhost port 8080 after 2256 ms: Could not connect to server
```

```powershell
docker port mission16-conflict
```

위 docker port 조회는 출력 없이 다음 입력 줄로 돌아왔다.

```powershell
docker inspect --format '{{json .HostConfig.PortBindings}}' mission16-conflict
```

```json
{"80/tcp":[{"HostIp":"","HostPort":"8080"}]}
```

설정에는 8080→80이 남아 있었지만 실제 공개 포트는 표시되지 않았고 HTTP 접속도 실패했다. 관찰한 사실을 기록하되 Docker 내부에서 이 상태가 발생한 구체적인 원인은 단정하지 않는다. 이번에는 재생성으로 해결했다. Up 표시만으로 포트 연결 성공을 판단할 수 없음을 확인했다.

## 6. 실습 컨테이너 재생성으로 포트 연결 복구

이번에 만든 mission16-conflict는 이미지의 정적 페이지를 제공하는 검증용 컨테이너이므로 해당 컨테이너만 중지·삭제하고 재생성했다. 기존 mission-web이나 볼륨은 삭제하지 않았다. 보존할 쓰기 계층 데이터가 있는 일반 컨테이너에 그대로 적용하는 절차는 아니다.

```powershell
docker stop mission16-conflict
```

```text
mission16-conflict
```

```powershell
docker rm mission16-conflict
```

```text
mission16-conflict
```

```powershell
docker run -d --name mission16-conflict -p 8080:80 mission-web:v1
```

```text
2778a1811e05ffebe4f9562809841b027cca236d8b689728cc5c132529915c7d
```

```powershell
docker port mission16-conflict
```

```text
80/tcp -> 0.0.0.0:8080
80/tcp -> [::]:8080
```

```powershell
curl.exe -i http://localhost:8080
```

실제 응답:

```http
HTTP/1.1 200 OK
Server: nginx/1.30.6
Date: Sun, 08 Nov 2026 12:23:39 GMT
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
```

## 7. 판정과 실습 종료 시점 상태

- 충돌 재현: 같은 호스트 8080 사용 요청에서 port is already allocated 확인.
- 점유 진단: Docker의 mission-web 포트와 Windows PID·프로세스 이름을 함께 확인.
- 포트 해제: mission-web 중지 뒤 Docker 목록과 Windows Listen 조회의 점유 부재 확인.
- 중간 실패: 최초 충돌 컨테이너를 start해도 실제 공개 포트가 없고 HTTP 연결 실패.
- 복구: 실습 컨테이너만 재생성하고 실제 8080→80 공개, HTTP 200과 직접 작성한 HTML 확인.
- 종료 시점: mission-web은 중지, 새 mission16-conflict(2778a1811e05...)는 8080에 실행 중이다. 그 이후 정리·원상복구까지 수행했다고 기록하지 않는다.

아래는 추가 정리가 필요할 때의 안내이며, 위 실행 로그에 포함된 완료 작업은 아니다. 기록을 모두 저장한 뒤 필요에 따라 수행한다.

```powershell
docker stop mission16-conflict
docker rm mission16-conflict
docker start mission-web
docker port mission-web
curl.exe -i http://localhost:8080
```

기존 컨테이너나 프로세스를 임의 종료할 수 없는 상황에는 다른 빈 호스트 포트를 선택하는 대안이 있다. 예를 들어 -p 8083:80으로 실행하려면 먼저 8083의 점유도 조회해야 한다. 이 대안은 이번 실행 성공 증거가 아니라 대응 방법 설명이다.
