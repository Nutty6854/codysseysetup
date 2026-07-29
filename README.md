# 개발 워크스테이션 구축 미션

리눅스 CLI, Docker, Git/GitHub를 직접 설정하고 검증하여 누구나 같은 절차로 재현할 수 있는 로컬 개발 워크스테이션을 구축하는 프로젝트입니다.

터미널에서 파일과 권한을 다루고, OrbStack 기반 Docker 환경에서 컨테이너를 운영했습니다. 공식 NGINX 이미지를 기반으로 정적 웹 서버 이미지를 제작하고 포트 매핑, 바인드 마운트, Docker 볼륨을 실제 명령과 출력으로 검증했습니다.

## 1. 실행 환경

| 항목 | 환경 |
| --- | --- |
| OS | macOS 26.5.2 |
| Shell | zsh (`/bin/zsh`) |
| Terminal | macOS Terminal.app, VS Code 통합 터미널 |
| Docker | 29.4.0, OrbStack |
| Docker Engine | 29.4.0, aarch64 |
| Git | 2.55.0 |
| 기본 브랜치 | `main` |

상세한 버전 및 Docker 엔진 확인 결과는 [Docker 설치와 기본 운영](docs/02-docker-basics.md)에 기록했습니다.

## 2. 수행 체크리스트

평가 기준 18개를 네 영역으로 나누고, 각 항목의 상세 명령과 출력은 연결된 문서에 기록했습니다.

### 2-1. 기능 동작 검증

- [x] 폴더·파일 생성, 이동, 이름 변경과 삭제 수행 — [CLI 실습 근거](docs/01-cli-permissions.md)
- [x] 파일 및 디렉토리 권한 변경 전·후 확인 — [권한 실습 근거](docs/01-cli-permissions.md#4-2-권한-실습)
- [x] `docker --version`, `docker info`로 CLI와 엔진 상태 확인 — [Docker 점검 근거](docs/02-docker-basics.md)
- [x] `docker run hello-world` 정상 실행 — [hello-world 근거](docs/02-docker-basics.md#hello-world-컨테이너-실행)
- [x] 이미지·컨테이너 목록 확인과 컨테이너 정리 — [기본 운영 근거](docs/02-docker-basics.md), [정리 근거](docs/03-custom-nginx-port.md#컨테이너-정리)
- [x] Dockerfile 기반 커스텀 이미지 빌드 — [빌드 근거](docs/03-custom-nginx-port.md#커스텀-이미지-빌드)
- [x] `localhost:8080` 포트 매핑 접속 및 브라우저·`curl` 검증 — [접속 근거](docs/03-custom-nginx-port.md#4-6-포트-매핑-및-웹페이지-접속-확인)
- [x] 컨테이너 삭제 후 Docker 볼륨 데이터 유지 — [영속성 근거](docs/04-storage.md#4-8-볼륨-영속성)
- [x] Git 설정, GitHub 원격 저장소와 VS Code 연동 확인 — [Git/GitHub 근거](docs/05-git-github.md)

### 2-2. 동작 구조 설계

- [x] CLI 실습, Docker 빌드 파일과 문서를 역할별 디렉토리로 분리하고 구성 기준 설명 — [프로젝트 구조](#4-프로젝트-구조)
- [x] 포트·바인드 마운트·볼륨을 명령과 결과로 재현 가능하게 정리 — [포트 재현](docs/03-custom-nginx-port.md), [스토리지 재현](docs/04-storage.md)

### 2-3. 핵심 기술 원리 적용

- [x] 이미지와 컨테이너를 빌드·실행·변경 관점으로 구분 — [Docker 원리](docs/02-docker-basics.md#이미지와-컨테이너의-차이)
- [x] 네트워크 격리와 포트 매핑이 필요한 이유 설명 — [포트 매핑 원리](docs/03-custom-nginx-port.md#포트-매핑이-필요한-이유)
- [x] 절대·상대 경로의 선택 기준과 호스트·컨테이너 경로 사례 설명 — [경로 선택 기준](docs/01-cli-permissions.md#절대-경로와-상대-경로)
- [x] `755`, `700`, `644`의 `r/w/x` 숫자 계산 규칙 설명 — [권한 숫자 규칙](docs/01-cli-permissions.md#4-2-권한-실습)

### 2-4.

- [x] 포트 충돌 시 Docker 포트 확인 → `lsof`로 프로세스 확인 → 호스트 포트 변경 순서 설명 — [포트 충돌 진단](docs/03-custom-nginx-port.md#포트-충돌-진단)
- [x] 컨테이너 삭제에 따른 데이터 손실 방지책으로 바인드 마운트·볼륨·백업 설명 — [스토리지 선택과 백업](docs/04-storage.md#바인드-마운트와-볼륨-선택-기준)
- [x] 가장 어려웠던 Git 저장소 경계 문제를 가설 → 확인 → 조치 순서로 설명 — [트러블슈팅 사례](docs/06-troubleshooting.md#4-상위-폴더가-git-저장소로-잘못-인식됨)

### 추가 제출 확인

- [x] 브라우저 주소창의 `localhost:8080`과 응답 화면이 함께 보이는 캡처 첨부
- [x] VS Code GitHub 로그인 및 저장소 연동 캡처 첨부
- [x] 실제 사용 환경을 macOS Terminal.app과 VS Code 통합 터미널로 기록

## 3. 상세 수행 문서

README에서 모든 수행 결과로 바로 이동할 수 있도록 주제별 문서를 분리했습니다.

| 문서 | 주요 내용 | 핵심 검증 |
| --- | --- | --- |
| [CLI와 파일 권한 실습](docs/01-cli-permissions.md) | 생성·복사·이동·삭제, 절대·상대 경로, `chmod` | `pwd`, `ls -la`, 권한 변경 전·후 |
| [Docker 설치와 기본 운영](docs/02-docker-basics.md) | OrbStack, hello-world, Ubuntu, 컨테이너 운영 | `docker info`, `ps -a`, `logs`, `stats`, `exec` |
| [커스텀 NGINX 이미지와 포트 매핑](docs/03-custom-nginx-port.md) | Dockerfile, 이미지 빌드, NGINX 실행, 포트 공개 | `docker build`, `docker port`, `curl`, 브라우저 |
| [바인드 마운트와 Docker 볼륨](docs/04-storage.md) | 호스트 변경 반영, 컨테이너 삭제 후 데이터 유지 | 변경 전·후 `curl`, 새 컨테이너에서 파일 확인 |
| [Git 및 GitHub 연동](docs/05-git-github.md) | 사용자·브랜치 설정, remote, commit/push | `git config`, `remote -v`, `git log` |
| [트러블슈팅](docs/06-troubleshooting.md) | Docker 인식, 셸 입력, 경로 오타, Git 저장소 경계 | 문제 → 원인 가설 → 확인 → 해결 |

## 4. 프로젝트 구조

```text
codysseysetup/
├── README.md
├── .gitignore
├── cli-practice/
│   ├── hello.txt
│   └── script.sh
├── custom-nginx/
│   ├── Dockerfile
│   ├── app/
│   │   └── index.html
│   └── bind-app/
│       └── index.html
└── docs/
    ├── 01-cli-permissions.md
    ├── 02-docker-basics.md
    ├── 03-custom-nginx-port.md
    ├── 04-storage.md
    ├── 05-git-github.md
    ├── 06-troubleshooting.md
    └── images/
```

CLI·권한 실습과 Docker 웹 서버 파일을 분리하여 각 실습의 목적을 명확히 했습니다. Docker 빌드 파일은 `custom-nginx/`에 모아 빌드 컨텍스트를 작게 유지하고, 긴 실행 로그와 이미지는 `docs/`에 분리하여 README에서 빠르게 찾을 수 있도록 구성했습니다.

## 5. 빠른 재현 방법

### 커스텀 NGINX 이미지 빌드

```bash
git clone https://github.com/Nutty6854/codysseysetup.git
cd codysseysetup/custom-
docker build -t codyssey-nginx:1.0 .
```

### 포트 매핑 실행 및 확인

```bash
docker run -d --name codyssey-web -p 8080:80 codyssey-nginx:1.0
docker ps
curl -I http://localhost:8080
curl http://localhost:8080
```

브라우저에서는 `http://localhost:8080`으로 접속합니다. 8080번 포트가 사용 중이면 [포트 충돌 진단 절차](docs/03-custom-nginx-port.md#포트-충돌-진단)를 확인합니다.

### 실행 종료와 정리

```bash
docker stop codyssey-web
docker rm codyssey-web
```

바인드 마운트와 Docker 볼륨의 전체 재현 명령은 [스토리지 실습 문서](docs/04-storage.md)에 있습니다.

## 6. 핵심 개념

- 이미지와 컨테이너: 이미지는 Dockerfile로 빌드하는 읽기 전용 실행 템플릿이고, 컨테이너는 이미지로 생성해 실행하는 인스턴스입니다. 이미지에 `COPY`한 HTML을 바꾸려면 재빌드해야 하지만, 바인드 마운트 파일은 재빌드 없이 변경을 반영할 수 있습니다. 컨테이너의 쓰기 계층은 컨테이너 삭제 시 사라질 수 있습니다.
- 포트 매핑: 컨테이너는 호스트와 분리된 네트워크 네임스페이스에서 실행되므로 호스트가 컨테이너 내부 80번 포트에 직접 접근할 수 없습니다. `-p <host>:<container>`로 호스트에 접근 지점을 만들어 요청을 전달합니다.
- 바인드 마운트: 호스트 경로를 직접 연결하므로 소스나 설정 파일의 변경을 즉시 반영하는 개발 환경에 적합합니다.
- Docker 볼륨: Docker가 저장 위치를 관리하며 컨테이너 수명과 분리되므로 데이터베이스나 업로드 파일 같은 영속 데이터에 적합합니다.
- Git과 GitHub: Git은 로컬 변경 이력을 관리하고, GitHub는 Git 저장소를 원격에서 공유해 백업과 협업을 지원합니다.
- 절대·상대 경로: 절대 경로는 파일 시스템 시작점부터 위치를 나타내고, 상대 경로는 현재 작업 디렉토리를 기준으로 대상을 찾습니다.

## 7. 보안 및 제출 전 확인

- 이메일, 토큰, 비밀번호, 개인키와 인증 코드는 문서 및 이미지에 노출하지 않습니다.
- 개발 중 로컬 접속만 필요하면 `-p 127.0.0.1:8080:80`처럼 바인딩 범위를 제한합니다.
- 브라우저 주소창의 `localhost:8080`과 NGINX 응답 화면이 함께 보이는 접속 증거를 첨부했습니다.
- VS Code 연동 화면에는 GitHub 계정, `codysseysetup` 저장소와 `main` 브랜치가 보이며 민감정보는 포함하지 않습니다.
- 모든 상세 문서와 이미지가 README 링크를 통해 열리는지 확인합니다.

## 8. 저장소

- GitHub: [Nutty6854/codysseysetup](https://github.com/Nutty6854/codysseysetup)
- 원격 저장소: `https://github.com/Nutty6854/codysseysetup.git`
