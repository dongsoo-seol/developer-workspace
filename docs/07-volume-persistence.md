# 7단계. Docker 볼륨 데이터 영속성 검증

## 1. 실습 목표와 판정

이름 있는 볼륨에 파일을 저장한 첫 컨테이너를 삭제하고, 새 컨테이너에 같은 볼륨을 연결해 이전 파일을 읽었다. 새 검증 컨테이너는 읽기 전용으로 연결하여 파일을 다시 작성하지 않고 기존 데이터만 확인했다.

**결과:** 첫 컨테이너 삭제 후에도 새 컨테이너에서 Mission 7 persistent data가 그대로 출력됐다. 이름 있는 볼륨의 데이터가 해당 컨테이너와 독립적으로 유지되는 것을 확인했다.

이 문서는 학습자가 로컬 WSL2 Ubuntu에서 실행해 첨부한 전체 명령·출력과 대화 기록에 근거한다. 본문은 프롬프트와 다른 실습 목록을 제거하거나 발췌했다. [전체 실행 기록의 공개용 사본](evidence/07-volume-session.md)에 컴퓨터명을 마스킹한 원래 순서를 보존했다.

## 2. 실습 환경과 대상

| 항목 | 실제 값 |
|---|---|
| 실습일 | 2026-11-07, KST |
| 환경 | Windows 10 Pro / WSL2 Ubuntu / Docker Desktop |
| Docker 명령 실행 위치 | WSL Ubuntu, 당시 현재 경로 /mnt/c/Users/user |
| 사용 이미지 | ubuntu:24.04 |
| 이름 있는 볼륨 | mission-data |
| 첫 컨테이너 | mission7-first, ID 9a7353841aec |
| 새 검증 컨테이너 | mission7-verify, ID 83b287f14feb |
| 연결 경로 | /data |
| 검증 파일 | /data/record.txt |

전체 기록에는 두 차례의 검증이 있다. 아래 표로 구분하며, 5절 이후 상세 기록은 후속 검증인 2회차를 기준으로 작성했다.

| 회차 | 삭제한 작성 컨테이너 | 삭제 후 생성한 검증 컨테이너 | 결과 |
|---|---|---|---|
| 1회차 | mission7-first, ID 2d37feccfaf1 | mission7-second, ID a62ed3789c49 | 같은 파일 내용 읽기 성공 |
| 2회차 | 다시 만든 mission7-first, ID 9a7353841aec | mission7-verify, ID 83b287f14feb | 읽기 전용으로 같은 내용 읽기 성공 |

같은 컨테이너 이름을 다시 사용했더라도 ID는 다르므로 두 작성 컨테이너를 하나로 취급하지 않는다. 후반에 이미 있던 mission7-second는 1회차에서 생성한 대상이었다. 2회차에서는 이를 새 대상이라고 재사용하지 않고 삭제 이후 별도로 생성한 mission7-verify로 검증했다.

<a id="verification"></a>

## 3. 요구사항별 완료 증거

| 완료 | 요구사항 | 증거 |
|---|---|---|
| [x] | 이름 있는 볼륨 생성·확인 | [볼륨 생성](#volume) |
| [x] | 첫 컨테이너 연결 | [첫 컨테이너](#first) |
| [x] | 데이터 저장·읽기 | [삭제 전 데이터](#write) |
| [x] | 첫 컨테이너 삭제 | [중지·삭제·목록 확인](#delete) |
| [x] | 삭제 후 볼륨 유지 | [볼륨 목록](#retained) |
| [x] | 새 컨테이너에 같은 볼륨 연결 | [새 검증 컨테이너](#verify) |
| [x] | 기존 데이터 확인 | [읽기 전용 재조회](#read) |
| [x] | 전후 비교와 개념 설명 | [비교표](#comparison), [저장 방식](#concepts) |

<a id="volume"></a>

## 4. 볼륨 생성과 조회

처음 조회한 docker volume ls에는 헤더만 있어 당시 볼륨이 없었다.

**실제 생성 명령과 출력**

```bash
docker volume create mission-data
```

```text
mission-data
```

**실제 조회 명령**

```bash
docker volume inspect mission-data
```

**실제 출력**

```json
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
```

볼륨 이름과 local 드라이버를 확인했다. CreatedAt은 UTC로 표시되며 한국 시각으로는 12:30:48 KST이다. Mountpoint는 Docker 엔진이 관리하는 내부 저장 위치이다. WSL의 현재 작업 디렉터리와 같은 위치라고 가정하거나 직접 들어가 수정하지 않았다.

전체 기록에서 2회차 시작 전에 같은 이름의 volume create를 다시 실행했다. 이때도 CreatedAt은 2026-11-07T03:30:48Z로 같았다. 기존 이름 있는 볼륨을 다시 사용한 것으로, 별도 새 볼륨을 만들거나 초기화한 결과로 설명하지 않는다.

### 4-1. 1회차 검증의 직접 증거

첫 작성 컨테이너의 실행 결과는 다음 ID였다.

```text
2d37feccfaf15900dec9bc42f769733eefa072b1330b590260edcf81a6cae6c4
```

해당 컨테이너에서 파일을 저장·읽은 뒤 아래 명령을 실행했다.

```bash
docker stop mission7-first
docker rm mission7-first
docker ps -a
docker volume ls
docker run -dit --name mission7-second --mount type=volume,source=mission-data,target=/data ubuntu:24.04 bash
```

stop·rm의 출력은 각각 mission7-first였고, 목록에서 작성 컨테이너가 사라진 뒤에도 볼륨은 남았다. 새 실행의 실제 ID는 다음과 같다.

```text
a62ed3789c49a8d1eb019dba2e58c0f89e3a43634df913286602bf9f0fc9c8ee
```

새 컨테이너의 실제 읽기 명령과 출력은 다음과 같다.

```bash
docker exec mission7-second cat /data/record.txt
```

```text
Mission 7 persistent data
```

따라서 1회차도 삭제 후 새 컨테이너에서 데이터가 유지됐다. 이후 mission7-second를 중지한 다음 2회차를 진행했다. 회차별 명령 사이의 전체 목록은 원본 공개용 사본에 있다.

<a id="first"></a>

## 5. 첫 번째 컨테이너에 연결

**실제 실행 명령**

```bash
docker run -dit --name mission7-first --mount type=volume,source=mission-data,target=/data ubuntu:24.04 bash
```

**실제 출력**

```text
9a7353841aec4859ee3e6055845a76f63ae5ec3468a63125e3c94e5589507c34
```

**실제 상태 조회**

```bash
docker ps
```

**해당 행 발췌**

```text
9a7353841aec   ubuntu:24.04   "bash"   10 seconds ago   Up 9 seconds   mission7-first
```

행의 빈 포트 열과 불필요한 표시 간격을 정리했다.

**실제 마운트 확인 명령**

```bash
docker inspect --format '{{json .Mounts}}' mission7-first
```

**실제 출력**

```json
[{"Type":"volume","Name":"mission-data","Source":"/var/lib/docker/volumes/mission-data/_data","Destination":"/data","Driver":"local","Mode":"z","RW":true,"Propagation":""}]
```

Type=volume, Name=mission-data, Destination=/data를 확인했다. RW=true이므로 이 연결을 통해 데이터를 저장할 수 있다.

<a id="write"></a>

## 6. 삭제 전 파일 저장·읽기

**실제 저장 명령**

```bash
docker exec mission7-first sh -c 'echo "Mission 7 persistent data" > /data/record.txt'
```

저장 명령 자체에는 별도 출력이 없었다. sh -c 뒤의 명령이 컨테이너 내부에서 실행되어 볼륨이 연결된 경로에 파일을 작성했다.

**실제 읽기 명령**

```bash
docker exec mission7-first cat /data/record.txt
```

**실제 출력**

```text
Mission 7 persistent data
```

이 출력을 삭제 전 데이터 기준으로 보관했다.

<a id="delete"></a>

## 7. 첫 번째 컨테이너 중지와 삭제

**실제 중지 명령과 출력**

```bash
docker stop mission7-first
```

```text
mission7-first
```

이 시점에 조회한 docker ps -a에는 아직 다음 행이 있었다.

```text
9a7353841aec   ubuntu:24.04   "bash"   8 minutes ago   Exited (137) 8 seconds ago   mission7-first
```

따라서 중지만 된 상태를 삭제 완료로 판단하지 않았다. 별도로 삭제 명령을 실행했다.

**실제 삭제 명령과 출력**

```bash
docker rm mission7-first
```

```text
mission7-first
```

**실제 삭제 후 확인 명령**

```bash
docker ps -a
```

제공된 전체 목록에서 mission7-first와 ID 9a7353841aec가 사라졌음을 확인했다. 목록에는 다른 실습 컨테이너와 기존 mission7-second가 남아 있었다. 이 다른 컨테이너를 삭제한 것으로 기록하지 않았다.

137은 실제 중지 시 표시된 종료 코드이며 0으로 바꾸지 않았다. 이번 영속성 판정의 핵심은 그 뒤 rm 성공과 목록에서 대상이 사라진 사실이다.

<a id="retained"></a>

## 8. 첫 컨테이너 삭제 후 볼륨 유지 확인

**삭제 확인 이후, 새 검증 컨테이너 생성 전에 실행**

```bash
docker volume ls
```

**실제 출력**

```text
DRIVER    VOLUME NAME
local     mission-data
```

첫 컨테이너가 삭제된 뒤에도 이름 있는 볼륨이 남아 있었다.

<a id="verify"></a>

## 9. 새로운 검증 컨테이너 생성

**실제 실행 명령**

```bash
docker run -dit --name mission7-verify --mount type=volume,source=mission-data,target=/data,readonly ubuntu:24.04 bash
```

**실제 출력**

```text
83b287f14febbef3051f48e72c9c7faa8d9c7a0ec7c1006b9046750785ca6c79
```

첫 컨테이너와 다른 ID의 새 컨테이너이다. 이번에는 파일을 다시 쓰지 않고 확인할 목적으로 읽기 전용 연결을 사용했다.

**실제 마운트 조회**

```bash
docker inspect --format '{{json .Mounts}}' mission7-verify
```

**실제 출력**

```json
[{"Type":"volume","Name":"mission-data","Source":"/var/lib/docker/volumes/mission-data/_data","Destination":"/data","Driver":"local","Mode":"z","RW":false,"Propagation":""}]
```

이름과 경로가 같은 볼륨임을 확인했다. 읽기 전용 여부는 Mode 문자열을 추측하는 대신 RW=false로 확인했다.

<a id="read"></a>

## 10. 새 컨테이너에서 기존 데이터 읽기

**실제 실행 명령**

```bash
docker exec mission7-verify cat /data/record.txt
```

**실제 출력**

```text
Mission 7 persistent data
```

새 컨테이너에서 파일을 작성하는 명령을 실행하지 않았고, 마운트도 읽기 전용이었다. 첫 컨테이너에서 저장했던 내용이 그대로 출력돼 데이터 유지가 확인됐다.

<a id="comparison"></a>

## 11. 삭제 전후 비교

| 항목 | 파일 작성 컨테이너 | 삭제 후 새 검증 컨테이너 |
|---|---|---|
| 이름 | mission7-first | mission7-verify |
| ID | 9a7353841aec | 83b287f14feb |
| 이미지 | ubuntu:24.04 | ubuntu:24.04 |
| 볼륨 이름 | mission-data | mission-data |
| 연결 경로 | /data | /data |
| RW | true | false |
| 파일 작성 | 저장 명령 수행 | 재작성 없이 읽기만 수행 |
| 읽은 내용 | Mission 7 persistent data | Mission 7 persistent data |
| 첫 컨테이너의 상태 | 저장 시 실행 중 | 새 컨테이너 생성 전에 삭제 완료 |

같은 컨테이너를 중지·재시작한 결과가 아니다. 첫 컨테이너 삭제와 서로 다른 ID의 신규 생성, 같은 볼륨 연결, 동일 내용의 읽기 결과를 함께 증거로 사용했다.

<a id="concepts"></a>

## 12. 저장 방식과 영속성 해석

| 저장 방식 | 데이터 위치·관리 | 컨테이너 삭제와의 관계 |
|---|---|---|
| 컨테이너 쓰기 계층 | 해당 컨테이너의 변경 공간 | 컨테이너 삭제 시 그 계층의 데이터도 사라짐 |
| 바인드 마운트 | 사용자가 지정한 호스트 파일·디렉터리 | 호스트 경로의 데이터는 컨테이너와 별도 |
| 이름 있는 볼륨 | Docker가 관리하는 별도 저장 공간 | 컨테이너를 삭제해도 볼륨을 보관하면 데이터 유지 |

위 표는 저장 방식의 개념 비교이며, 이번 단계에서 실제 검증한 대상은 이름 있는 볼륨이다. 6단계에서는 바인드 마운트의 파일 변경 반영을 따로 확인했다.

볼륨이 필요한 이유는 교체·삭제될 수 있는 실행 환경과 계속 보관할 데이터를 분리하기 위해서이다. 이번에는 컨테이너가 바뀌어도 같은 볼륨의 파일을 읽을 수 있었다.

영속성은 백업과 다르다. 볼륨은 별도 복사본을 자동 생성하는 장치가 아니며, 볼륨 자체 삭제나 파일 손상까지 막는 것은 아니다. 중요한 데이터는 별도의 백업·복구 계획이 필요하다. 이번에 백업 작업을 실행한 기록은 없다.

<a id="troubleshooting"></a>

## 13. 수행 중 확인 사항

### 중지를 삭제로 혼동한 상태 확인

stop 이후 목록에 Exited 상태로 남아 있어 삭제가 아님을 확인했다. rm을 실행한 뒤 목록에서 사라진 것을 다시 확인했다. 중지와 삭제의 차이를 실제 상태로 구분했다.

### 이미 있는 두 번째 이름을 검증에 사용하지 않음

2회차 목록에는 1회차에 생성한 mission7-second가 이미 존재했다. 이를 2회차 삭제 이후 새로 만든 컨테이너라고 간주하지 않고, 삭제 확인 후 mission7-verify를 새로 만들었다. 각 회차에서 삭제보다 뒤에 생성된 대상의 읽기 결과를 사용했다.

### 문서 경로를 명령으로 입력한 오류

1회차 검증 뒤 아래 경로를 Ubuntu 명령 입력 줄에 직접 입력했다.

```text
docs/07-volume-persistence.md
-bash: docs/07-volume-persistence.md: No such file or directory
```

이 경로는 보고서를 저장할 위치를 뜻하며 셸 명령이 아니다. 해당 위치에 문서가 없는 상태에서 실행 대상으로 입력해 오류가 났다. 볼륨 검증 실패와는 별개의 문제다. 제출 묶음의 실제 Markdown 문서를 저장소 docs 폴더에 넣고 메모장이나 VS Code로 열어 편집하면 된다. 로컬 PC에서 문서 배치를 마쳤다는 후속 실행 기록은 아직 없다.

## 14. 완료 상태와 정리 방법

- [x] 이름 있는 볼륨 생성·조회
- [x] 첫 컨테이너에 연결 후 파일 작성·읽기
- [x] 첫 컨테이너 중지와 삭제, 목록에서 부재 확인
- [x] 삭제 후에도 볼륨이 존재함을 확인
- [x] 새로운 ID의 컨테이너에 같은 볼륨 연결
- [x] 읽기 전용 연결에서 재작성 없이 같은 파일 내용 확인
- [x] 저장 방식·영속성·백업 차이 설명

7단계 기능 실습은 완료했다. 마지막으로 제공된 결과는 새 검증 컨테이너에서의 파일 읽기 성공이며, 그 뒤 중지했다는 결과는 아직 없다.

실습 후 중지하려면 아래 명령을 사용할 수 있다. 정리 방법 안내이며 추가 실행 결과가 아니다.

```bash
docker stop mission7-verify
```

데이터를 보관하려면 mission-data 볼륨을 유지한다. 다음은 8단계의 Git 설정·GitHub·VS Code 연동 및 최종 제출이다.
