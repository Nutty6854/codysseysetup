# 개발 워크스테이션 구축 미션

리눅스 CLI, Docker, Git/GitHub를 직접 설정하고 검증하여 누구나 같은 절차로 재현할 수 있는 로컬 개발 워크스테이션을 구축하는 프로젝트입니다.

터미널에서 파일과 권한을 다루고, OrbStack 기반 Docker 환경에서 컨테이너를 운영했습니다. 공식 NGINX 이미지를 기반으로 정적 웹 서버 이미지를 제작하고 포트 매핑, 바인드 마운트, Docker 볼륨을 실제 명령과 출력으로 검증했습니다.

## 1. 실행 환경

| 항목 | 환경 |
| --- | --- |
| OS | macOS 26.5.2 |
| Shell | zsh (`/bin/zsh`) |
| Terminal | macOS 터미널 환경 — 최종 제출 전 실제 앱 이름 확인 |
| Docker | 29.4.0, OrbStack |
| Docker Engine | 29.4.0, aarch64 |
| Git | 2.55.0 |
| 기본 브랜치 | `main` |

상세한 버전 및 Docker 엔진 확인 결과는 [Docker 설치와 기본 운영](docs/02-docker-basics.md)에 기록했습니다.

## 2. 수행 체크리스트

- [x] 터미널 기본 명령과 파일 조작
- [x] 파일 및 디렉토리 권한 변경
- [x] Docker 설치 및 엔진 점검
- [x] `hello-world` 및 Ubuntu 컨테이너 실행
- [x] `docker images`, `ps`, `logs`, `stats`, `exec` 실습
- [x] NGINX 기반 커스텀 이미지 빌드
- [x] 포트 매핑 및 `curl` 접속
- [x] 바인드 마운트 변경 반영
- [x] Docker 볼륨 영속성
- [x] Git 설정 및 GitHub 원격 저장소 연동
- [ ] 주소창의 `localhost:8080`이 보이는 브라우저 캡처로 교체
- [ ] VS Code GitHub 로그인 화면 증거 첨부
- [ ] 실제 사용한 터미널 앱 이름 확인

## 3. 상세 수행 문서

README에서 모든 수행 결과로 바로 이동할 수 있도록 주제별 문서를 분리했습니다.

| 문서 | 주요 내용 | 핵심 검증 |
| --- | --- | --- |
| [CLI와 파일 권한 실습](docs/01-cli-permissions.md) | 생성·복사·이동·삭제, 절대·상대 경로, `chmod` | `pwd`, `ls -la`, 권한 변경 전·후 |
| [Docker 설치와 기본 운영](docs/02-docker-basics.md) | OrbStack, hello-world, Ubuntu, 컨테이너 운영 | `docker info`, `ps -a`, `logs`, `stats`, `exec` |
| [커스텀 NGINX 이미지와 포트 매핑](docs/03-custom-nginx-port.md) | Dockerfile, 이미지 빌드, NGINX 실행, 포트 공개 | `docker build`, `docker port`, `curl`, 브라우저 |
| [바인드 마운트와 Docker 볼륨]확인ocs/04-storage.md) | 호스트 변경 반영, 컨테이너 삭제 후 데이터 유지 | 변경 전·후 `curl`, 새 컨테이너에서 파일 확인 |
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
cd codysseysetup/custom-nginx
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

- 이미지와 컨테이너: 이미지는 읽기 전용 실행 템플릿이고, 컨테이너는 이미지로 생성된 실행 인스턴스입니다. 컨테이너의 쓰기 계층은 컨테이너 삭제 시 사라질 수 있습니다.
- 포트 매핑: 컨테이너는 호스트와 분리된 네트워크 환경에서 실행되므로 `-p <host>:<container>`로 접근 경로를 연결합니다.
- 바인드 마운트: 호스트 경로를 직접 연결하므로 소스나 설정 파일의 변경을 즉시 반영하는 개발 환경에 적합합니다.
- Docker 볼륨: Docker가 저장 위치를 관리하며 컨테이너 수명과 분리되므로 데이터베이스나 업로드 파일 같은 영속 데이터에 적합합니다.
- Git과 GitHub: Git은 로컬 변경 이력을 관리하고, GitHub는 Git 저장소를 원격에서 공유해 백업과 협업을 지원합니다.
- 절대·상대 경로: 절대 경로는 파일 시스템 시작점부터 위치를 나타내고, 상대 경로는 현재 작업 디렉토리를 기준으로 대상을 찾습니다.

## 7. 보안 및 제출 전 확인

- 이메일, 토큰, 비밀번호, 개인키와 인증 코드는 문서 및 이미지에 노출하지 않습니다.
- 개발 중 로컬 접속만 필요하면 `-p 127.0.0.1:8080:80`처럼 바인딩 범위를 제한합니다.
- 브라우저 증거에는 주소창의 `localhost:8080`과 응답 화면이 함께 보여야 합니다.
- VS Code 연동 화면은 저장소 연결 상태만 보이도록 하고 계정의 민감정보는 가립니다.
- 모든 상세 문서와 이미지가 README 링크를 통해 열리는지 확인합니다.

## 8. 저장소

- GitHub: [Nutty6854/codysseysetup](https://github.com/Nutty6854/codysseysetup)
- 원격 저장소: `https://github.com/Nutty6854/codysseysetup.git`
