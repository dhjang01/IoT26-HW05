# HW5 Getting Started with Home Assistant on Raspberry Pi

## 과제명

HW5 Getting Started with Home Assistant on Raspberry Pi

## 목표

Raspberry Pi OS 환경을 유지한 상태에서 Docker로 Home Assistant를 설치하고, Helper Toggle과 Automation을 이용한 간단한 홈 자동화 예제를 구성한다.

## 환경

- Raspberry Pi username: `aiot`
- Raspberry Pi IP: `172.20.10.2`
- 작업 방식: SSH
- 프로젝트 경로: `/home/aiot/IoT26-HW05`
- Home Assistant config 경로: `/home/aiot/homeassistant/config`
- 실행 방식: Docker Compose
- 접속 주소: `http://172.20.10.2:8123`

## 설치 방법

### 1. Docker 설치 여부 확인

```bash
docker --version
```

```bash
docker compose version
```

Docker가 없으면 아래 명령어를 한 줄씩 실행한다.

```bash
sudo apt update
```

```bash
sudo apt install -y ca-certificates curl gnupg
```

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
```

```bash
sudo sh get-docker.sh
```

### 2. aiot 사용자를 docker 그룹에 추가

```bash
sudo usermod -aG docker aiot
```

그룹 적용을 위해 SSH를 종료했다가 다시 접속한다.

```bash
exit
```

다시 접속 후 확인한다.

```bash
groups
```

```bash
docker ps
```

### 3. 프로젝트 폴더 준비

```bash
mkdir -p /home/aiot/IoT26-HW05/screenshots
```

```bash
mkdir -p /home/aiot/homeassistant/config
```

## 실행 방법

```bash
cd /home/aiot/IoT26-HW05
```

```bash
docker compose up -d
```

실행 확인:

```bash
docker ps
```

로그 확인:

```bash
docker logs -f homeassistant
```

컨테이너 중지:

```bash
docker compose down
```

## 접속 주소

브라우저에서 아래 주소로 접속한다.

```text
http://172.20.10.2:8123
```

초기 실행 직후에는 Home Assistant 준비에 몇 분 정도 걸릴 수 있다.

## Home Assistant 초기 설정 방법

1. 브라우저에서 `http://172.20.10.2:8123`에 접속한다.
2. `Create my smart home` 또는 계정 생성 화면에서 관리자 계정을 만든다.
3. 이름, 사용자명, 비밀번호를 입력한다.
4. 위치, 시간대, 단위 설정을 확인한다.
5. 자동으로 발견된 기기가 있으면 건너뛰거나 필요한 항목만 추가한다.
6. Dashboard 화면이 보이면 초기 설정 완료이다.

## Helper Toggle 생성 방법

1. Home Assistant 왼쪽 메뉴에서 `Settings`로 이동한다.
2. `Devices & services`를 연다.
3. 상단 또는 하단의 `Helpers`를 선택한다.
4. `Create Helper`를 누른다.
5. `Toggle`을 선택한다.
6. 이름을 `Raspberry Pi Test Switch`로 입력한다.
7. 생성 후 Dashboard에 추가하거나 Entities 목록에서 확인한다.

## Automation 생성 방법

목표:

- Trigger: `Raspberry Pi Test Switch`가 ON이 될 때
- Action: Persistent Notification 생성
- Message: `HW5 Home Assistant automation triggered successfully.`

UI 설정:

1. `Settings`로 이동한다.
2. `Automations & scenes`를 선택한다.
3. `Create Automation`을 누른다.
4. `Create new automation`을 선택한다.
5. Trigger에서 `Entity` 또는 `State`를 선택한다.
6. Entity는 `Raspberry Pi Test Switch`를 선택한다.
7. From은 `off`, To는 `on`으로 설정한다.
8. Action에서 `Call service`를 선택한다.
9. Service는 `Persistent Notification: Create`를 선택한다.
10. Message에 아래 문장을 입력한다.

```text
HW5 Home Assistant automation triggered successfully.
```

11. Automation 이름을 저장한다.
12. Dashboard에서 스위치를 ON으로 바꾸고 알림이 뜨는지 확인한다.

## docker-compose.yml 설명

```yaml
services:
  homeassistant:
    container_name: homeassistant
    image: ghcr.io/home-assistant/home-assistant:stable
    network_mode: host
    environment:
      - TZ=Asia/Seoul
    volumes:
      - /home/aiot/homeassistant/config:/config
    restart: unless-stopped
```

- `ghcr.io/home-assistant/home-assistant:stable`: Home Assistant stable 이미지 사용
- `network_mode: host`: Raspberry Pi의 네트워크를 그대로 사용
- `TZ=Asia/Seoul`: 시간대를 한국으로 설정
- `/home/aiot/homeassistant/config:/config`: 설정 파일을 호스트에 저장
- `restart: unless-stopped`: 재부팅 후에도 자동 실행

## Troubleshooting

### `docker: command not found`

Docker가 설치되지 않은 상태이다.

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
```

```bash
sudo sh get-docker.sh
```

### `permission denied while trying to connect to the Docker daemon socket`

`aiot` 사용자가 docker 그룹에 적용되지 않은 상태이다.

```bash
sudo usermod -aG docker aiot
```

SSH를 종료했다가 다시 접속한다.

```bash
exit
```

### `docker compose` 명령이 동작하지 않음

Docker Compose plugin이 없을 수 있다.

```bash
sudo apt install -y docker-compose-plugin
```

### `http://172.20.10.2:8123` 접속이 안 됨

컨테이너 상태를 확인한다.

```bash
docker ps
```

로그를 확인한다.

```bash
docker logs homeassistant
```

포트가 열렸는지 확인한다.

```bash
ss -lntp | grep 8123
```

초기 실행 중이면 몇 분 기다린 뒤 다시 접속한다.

### 컨테이너 다시 시작

```bash
cd /home/aiot/IoT26-HW05
```

```bash
docker compose restart
```

## 최종 파일 구조

```text
IoT26-HW05/
├── README.md
├── docker-compose.yml
└── screenshots/
```

실행 중 생성되는 Home Assistant 설정 폴더:

```text
/home/aiot/homeassistant/config
```

## 실제 실행 명령어 요약

```bash
sudo apt update
```

```bash
sudo apt install -y ca-certificates curl gnupg
```

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
```

```bash
sudo sh get-docker.sh
```

```bash
sudo usermod -aG docker aiot
```

```bash
exit
```

SSH 재접속 후:

```bash
mkdir -p /home/aiot/IoT26-HW05/screenshots
```

```bash
mkdir -p /home/aiot/homeassistant/config
```

```bash
cd /home/aiot/IoT26-HW05
```

```bash
docker compose up -d
```

```bash
docker ps
```

브라우저 접속:

```text
http://172.20.10.2:8123
```

결과물



https://github.com/user-attachments/assets/def5e825-1f79-4372-ba96-aedaca2b4f18



<img width="2252" height="4000" alt="IoT26-HW05-1" src="https://github.com/user-attachments/assets/61abde57-715a-4382-ad54-bd1c1978ff88" />
<img width="2252" height="4000" alt="IoT26-HW05-2" src="https://github.com/user-attachments/assets/59e20861-ff9c-469d-b947-cd5e040ecf77" />


