# codysseysetup

# 개발 워크스테이션 구축 미션

## 1. 프로젝트 개요
코디세이 처음을 위한 환경세팅 방법

## 2. 실행 환경

- **OS**: macOS 26.5.2
- **Shell**: zsh (`/bin/zsh`)
- **Terminal**: (예: iTerm2 / Terminal.app)
- **Docker**: 29.4.0 (OrbStack 기반)
- **Git**: 2.55.0

### 확인 명령
​```bash
$ sw_vers
-ProductName:    macOS
-ProductVersion: 26.5.2

$ echo $SHELL
/bin/zsh

$ docker --version
Docker version 29.4.0, build ...

$ git --version
git version 2.55.0
​```

## 3. 수행 체크리스트
- [x] 터미널 기본 명령
- [x] 파일/디렉토리 권한 실습
- [x] Docker 설치 및 점검
- [x] Docker 기본 운영 명령
- [x] hello-world / ubuntu 실행
- [ ] 커스텀 이미지 빌드
- [ ] 포트 매핑 접속
- [ ] 바인드 마운트
- [ ] 볼륨 영속성
- [ ] Git 설정 및 GitHub 연동

## 4. 실습 로그
### 4-1. 터미널 조작
#### 터미널 기본 명령 실습
1) 현재 위치확인
% pwd 
결과: /Users/wonjun/Developer

2) 목록 확인 (숨김파일 포함)
% ls -la

결과:
total 24
drwxr-xr-x   4 wonjun  staff    128 Jul 27 18:31 .
drwxr-x---+ 30 wonjun  staff    960 Jul 27 18:57 ..
-rw-r--r--@  1 wonjun  staff  10244 Jul 27 18:36 .DS_Store
drwxr-xr-x   4 wonjun  staff    128 Jul 27 18:36 codyssey

3) 실습용 디렉토리 생성 및 이동
mkdir cli-practice
cd cli-practice

결과:
% pwd
/Users/wonjun/DEVELOPER/codyssey/codysseysetup

4) 빈 파일 생성
touch hello.txt notes.txt

결과:
% ls
hello.txt       notes.txt

5) 파일 내용 작성 및 확인
echo "Hello, Docker!" > hello.txt

결과:
% cat hello.txt
Hello, Docker



6) 하위 디렉토리 생성
mkdir backup
결과:

% ls -la
total 8
drwxr-xr-x@ 2 wonjun  staff   64 Jul 27 20:09 backup

7) 복사
cp hello.txt backup/hello_copy.txt

결과:
% ls backup
hello_copy.txt

8) 이동/이름변경
mv notes.txt memo.txt

결과:
% ls
backup          hello.txt       memo.txt

9) 삭제
rm memo.txt
rm -r backup    # 디렉토리 삭제 (재귀)

결과:
% ls
hello.txt

### 4-2. 권한 실습
파일과 디렉토리의 권한을 확인하고 chmod 명령어를 사용하여 권한을 변경했습니다.

1. 실습 파일 및 디렉토리 생성
$ touch script.sh
$ mkdir secret
생성된 항목을 확인했습니다.
$ ls -la
실행 결과:
```
total 8
drwxr-xr-x  5 wonjun  staff  160 Jul 27 22:44 .
drwxr-xr-x@ 5 wonjun  staff  160 Jul 27 19:50 ..
-rw-r--r--@ 1 wonjun  staff   14 Jul 27 20:07 hello.txt
-rw-r--r--@ 1 wonjun  staff    0 Jul 27 22:44 script.sh
drwxr-xr-x@ 2 wonjun  staff   64 Jul 27 22:44 secret
```
2. 변경 전 권한 확인
$ ls -l script.sh
$ ls -ld secret
실행 결과:
% ls -l script.sh
-rw-r--r--@ 1 wonjun  staff  0 Jul 27 22:44 script.sh
% ls -ld secret
drwxr-xr-x@ 2 wonjun  staff  64 Jul 27 22:44 secret

script.sh 앞의 -는 일반 파일을 의미하고, secret 앞의 d는 디렉토리를 의미합니다.
권한은 소유자, 그룹, 기타 사용자 순서로 표시됩니다.
소유자 / 그룹 / 기타 사용자
각 권한 문자는 다음 의미를 가집니다.
r: 읽기 권한
w: 쓰기 권한
x: 실행 또는 디렉토리 접근 권한
-: 권한 없음


3. 파일 권한을 755로 변경
$ chmod 755 script.sh
$ ls -l script.sh
실행 결과:
% chmod 755 script.sh
% ls -l script.sh
-rwxr-xr-x@ 1 wonjun  staff  0 Jul 27 22:44 script.sh

755는 다음 권한을 의미합니다.
소유자: 읽기, 쓰기, 실행
그룹: 읽기, 실행
기타 사용자: 읽기, 실행
문자 권한으로 표현하면 다음과 같습니다.
rwxr-xr-x
숫자 권한은 읽기 4, 쓰기 2, 실행 1을 더하여 계산합니다.
7 = 4 + 2 + 1
5 = 4 + 1
5 = 4 + 1


4. 디렉토리 권한을 700으로 변경
$ chmod 700 secret
$ ls -ld secret
실행 결과:
% ls -ld secret
drwx------@ 2 wonjun  staff  64 Jul 27 22:44 secret

700은 소유자에게만 읽기, 쓰기, 접근 권한을 부여하고 그룹 및 기타 사용자의 접근은 허용하지 않는 권한입니다.
rwx------
디렉토리에서 실행 권한 x는 해당 디렉토리로 이동하거나 내부 항목에 접근할 수 있는 권한을 의미합니다.


5. 파일 권한을 644로 변경
$ chmod 644 script.sh
$ ls -l script.sh
실행 결과:
% ls -l script.sh    
-rw-r--r--@ 1 wonjun  staff  0 Jul 27 22:44 script.sh

644는 다음 권한을 의미합니다.
소유자: 읽기, 쓰기
그룹: 읽기
기타 사용자: 읽기
문자 권한으로 표현하면 다음과 같습니다.
rw-r--r--
644는 일반 문서나 설정 파일에 자주 사용되는 권한입니다. 소유자는 파일을 수정할 수 있고, 다른 사용자는 읽기만 가능합니다.

6.권한 실습 결과
이번 실습을 통해 파일과 디렉토리의 권한을 ls -l로 확인하고, chmod를 이용해 숫자 방식으로 변경하는 방법을 확인했습니다.
755: 소유자는 모든 권한을 가지고, 다른 사용자는 읽기와 실행이 가능합니다.
700: 소유자만 해당 파일 또는 디렉토리에 접근할 수 있습니다.
644: 소유자는 읽기와 쓰기가 가능하고, 다른 사용자는 읽기만 가능합니다.

### 4-3. Docker 설치 및 기본 점검

OrbStack을 기반으로 설치한 Docker CLI와 Docker 엔진의 동작 상태를 확인했습니다.

#### Docker 버전 확인

```bash
$ docker --version
Docker version 29.4.0, build 9d7ad9f
```

`docker --version` 명령을 통해 현재 설치된 Docker CLI 버전을 확인했습니다.

#### Docker 엔진 동작 확인

```bash
$ docker info
Client:
 Version:    29.4.0
 Context:    orbstack
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.33.0
    Path:     /Users/wonjun/.docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.1.2
    Path:     /Users/wonjun/.docker/cli-plugins/docker-compose
    ... 
```
> `docker info`의 전체 출력 중 주요 부분만 기록했습니다.

`docker info` 명령에서 Client와 Server 정보가 모두 출력되는 것을 확인했습니다. 이를 통해 Docker CLI가 Docker 엔진과 정상적으로 연결된 상태임을 확인했습니다.

#### hello-world 컨테이너 실행

Docker 설치와 이미지 다운로드, 컨테이너 생성 및 실행 과정이 정상적으로 동작하는지 확인하기 위해 `hello-world` 이미지를 실행했습니다.

```bash
$ docker run --name hello-container hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
58dee6a49ef1: Pull complete 
c3bdf82c34d1: Download complete 
Digest: sha256:c3cbe1cc1aa588a64951ac6286e0df7b27fe2e6324b1001c619bb358770c0178
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
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
```

출력에서 다음 메시지를 확인했습니다.

```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

이 과정에서 로컬에 `hello-world` 이미지가 없었기 때문에 Docker가 이미지를 다운로드한 뒤, 해당 이미지로 컨테이너를 생성하고 실행했습니다. 컨테이너는 메시지를 출력한 후 정상 종료되었습니다.

#### 이미지 목록 확인

```bash
% docker images
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   c3cbe1cc1aa5       18.5kB         10.3kB    U   
```

`docker images` 명령을 통해 다운로드된 `hello-world` 이미지를 확인했습니다.

#### 실행 중인 컨테이너 확인

```bash
% docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

`hello-world` 컨테이너는 메시지를 출력한 후 종료되기 때문에 실행 중인 컨테이너 목록에는 표시되지 않았습니다.

#### 전체 컨테이너 확인

```bash
% docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
2daaee979015   hello-world   "/hello"   2 minutes ago   Exited (0) 2 minutes ago             hello-container
```

`docker ps -a` 명령을 사용하자 종료된 `hello-container` 컨테이너가 표시되었습니다. 상태의 `Exited (0)`은 컨테이너가 오류 없이 정상적으로 종료되었음을 의미합니다.

#### 컨테이너 로그 확인

```bash
% docker logs hello-container
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
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

```

컨테이너가 종료된 이후에도 `docker logs` 명령을 사용하여 실행 당시 출력된 메시지를 확인할 수 있었습니다.

#### 이미지와 컨테이너의 차이

Docker 이미지는 컨테이너를 생성하기 위한 읽기 전용 실행 환경입니다. 컨테이너는 이미지를 기반으로 실제 생성되고 실행되는 인스턴스입니다.

같은 이미지 하나를 이용하여 여러 개의 컨테이너를 생성할 수 있습니다.

### 4-4. Ubuntu 컨테이너 실행 및 내부 명령 실습
Ubuntu 이미지를 사용하여 컨테이너를 실행하고, 컨테이너 내부에서 기본적인 Linux 명령어를 수행했습니다.

Ubuntu 컨테이너 실행 및 내부 진입
```bash
% ​docker run -it --name ubuntu-practice ubuntu bash
Unable to find image 'ubuntu:latest' locally latest: Pulling from library/ubuntu
693710ba2039: Pull complete 
55237ac9880d: Pull complete 
fdfb14aa961e: Download complete 
Digest: sha256:실제 출력값 Status: Downloaded newer image for ubuntu:latest root@컨테이너ID:/#

```
"docker run" 명령으로 Ubuntu 이미지를 기반으로 새로운 컨테이너를 생성하고 실행했습니다.

사용한 옵션은 다음과 같습니다.
-i: 표준 입력을 열린 상태로 유지합니다.
-t: 터미널 환경을 제공합니다.
--name ubuntu-practice: 컨테이너 이름을 ubuntu-practice로 지정합니다.
ubuntu: 사용할 Docker 이미지입니다.
bash: 컨테이너에서 실행할 기본 명령입니다.

2)컨테이너 내부 현재 위치 확인

```bash
root@28c7aae53230:/# pwd 
/

컨테이너 내부의 현재 위치는 루트 디렉토리인 /로 확인되었습니다.
```

2-1)컨테이너 내부 파일 목록 확인
```bash
root@28c7aae53230:/# ls -la
total 12
drwxr-xr-x   1 root root   6 Jul 28 03:31 .
drwxr-xr-x   1 root root   6 Jul 28 03:31 ..
-rwxr-xr-x   1 root root   0 Jul 28 03:31 .dockerenv
drwxr-xr-x   1 root root  26 Jul 13 16:25 .rock
lrwxrwxrwx   1 root root   7 Apr 20 08:46 bin -> usr/bin
....
```
ls -la 명령을 통해 Ubuntu 컨테이너 내부의 디렉토리와 숨김 파일을 포함한 전체 목록을 확인했습니다.

2-2)Ubuntu 운영체제 정보 확인

```bash

root@28c7aae53230:/# cat /etc/os-release 
PRETTY_NAME="Ubuntu 26.04 LTS"
NAME="Ubuntu"
VERSION_ID="26.04"
VERSION="26.04 LTS (Resolute Raccoon)"
VERSION_CODENAME=resolute
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=resolute
LOGO=ubuntu-logo

/etc/os-release 파일을 확인하여 현재 컨테이너가 Ubuntu 환경에서 실행되고 있음을 확인했습니다.
```

2-3)문자열 출력 확인

```bash
root@28c7aae53230:/# echo "Hello from Ubuntu container"
Hello from Ubuntu container

echo 명령으로 컨테이너 내부에서 문자열이 정상적으로 출력되는 것을 확인했습니다.
```

2-4)디렉토리와 파일 생성
```bash
root@28c7aae53230:/# mkdir docker-practice
root@28c7aae53230:/# touch docker-practice/test.txt

docker-practice 디렉토리를 생성한 뒤, 해당 디렉토리 안에 test.txt 파일을 생성했습니다.
```

2-5)파일 내용 작성 및 확인
```bash
root@28c7aae53230:/# echo "Container data" > docker-practice/test.txt
root@28c7aae53230:/# cat docker-practice/test.txt
Container data

echo 명령과 출력 리다이렉션 기호인 >를 사용하여 파일에 내용을 작성하고, cat 명령으로 저장된 내용을 확인했습니다.
```

2-6)컨테이너 종료
```bash
root@28c7aae53230:/# exit
exit

컨테이너 내부에서 exit를 실행하자 bash 프로세스가 종료되었고, Ubuntu 컨테이너도 함께 중지되었습니다.
```

3)실행 중인 컨테이너 확인
```bash
$ docker ps
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS   PORTS   NAMES

ubuntu-practice 컨테이너는 종료된 상태이므로 실행 중인 컨테이너 목록에는 표시되지 않았습니다.
```

3-1)전체 컨테이너 확인
```bash
wonjun@wonjunui-MacBookAir ~ % docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
28c7aae53230   ubuntu        "bash"     30 minutes ago   Exited (0) 30 seconds ago             ubuntu-practice
2daaee979015   hello-world   "/hello"   14 hours ago     Exited (0) 14 hours ago               hello-container

- docker ps -a 명령을 사용하여 종료된 ubuntu-practice 컨테이너를 확인했습니다.
- 상태의 Exited (0)은 컨테이너가 오류 없이 정상적으로 종료되었음을 의미합니다.
```

3-2)종료된 컨테이너 다시 시작
```bash
$ docker start -ai ubuntu-practice
root@28c7aae53230:/#

docker start -ai 명령으로 기존에 생성된 ubuntu-practice 컨테이너를 다시 시작하고 내부 터미널에 연결했습니다.
-a: 컨테이너의 출력에 연결합니다.
-i: 컨테이너의 입력을 사용할 수 있도록 연결합니다.
```

3-3)기존 파일 유지 여부 확인

```bash
root@28c7aae53230:/# cat /docker-practice/test.txt
Container data

컨테이너를 중지한 뒤 다시 시작해도 이전에 생성한 파일과 내용이 그대로 유지되는 것을 확인했습니다.
이는 컨테이너가 삭제된 것이 아니라 중지되었다가 다시 실행되었기 때문입니다.
```

3-5)확인 후 컨테이너를 다시 종료했습니다.
```bash
root@컨테이너ID:/# exit
exit
```
4) 백그라운드 컨테이너 및 Docker 기본 운영 명령 실습

Ubuntu 컨테이너를 백그라운드에서 실행하고, 실행 중인 컨테이너에 docker exec 명령으로 진입했습니다. 이후 로그와 리소스 사용량을 확인하고 컨테이너를 중지했습니다.

4-1)벡그라운드 컨테이너 실행
```bash
docker run -d --name ubuntu-background ubuntu sleep infinity

d8e2b0e9f85fc5347fa2a822740a83030b2abd623744f4bbd3877a463e44ca38
w

-d 옵션을 사용하여 Ubuntu 컨테이너를 백그라운드에서 실행했습니다.
컨테이너가 바로 종료되지 않도록 기본 프로세스로 sleep infinity를 실행했습니다.
-d: 컨테이너를 백그라운드에서 실행합니다.
--name ubuntu-background: 컨테이너 이름을 지정합니다.
sleep infinity: 종료되지 않고 계속 대기하는 프로세스를 실행합니다.

```
4-2)실행중인 컨테이너 확인

```bash
docker ps 
CONTAINER ID   IMAGE     COMMAND            CREATED         STATUS         PORTS     NAMES
d8e2b0e9f85f   ubuntu    "sleep infinity"   2 minutes ago   Up 2 minutes             ubuntu-background

docker ps 명령을 통해 ubuntu-background 컨테이너가 Up 상태로 실행 중인 것을 확인했습니다.
```

4-3) docker exec로 컨테이너 내부 진입
```bash
docker exec -it ubuntu-background bash
root@d8e2b0e9f85f:/#  

docker exec 명령을 사용하여 이미 실행 중인 컨테이너 안에서 새로운 bash 프로세스를 실행했습니다.
```
4-4)컨테이너 내부 명령 실행
```bash
root@실제컨테이너ID:/# pwd
/

root@실제컨테이너ID:/# echo "Entered with docker exec"
Entered with docker exec

root@실제컨테이너ID:/# touch exec-test.txt

root@실제컨테이너ID:/# ls -l exec-test.txt

- 실제 출력 결과:
root@d8e2b0e9f85f:/# pwd
/
root@d8e2b0e9f85f:/# echo "Entered with docker exec" Entered with docker exec
Entered with docker exec Entered with docker exec
root@d8e2b0e9f85f:/# touch exec-test.txt
root@d8e2b0e9f85f:/# ls -l exec-test.txt
-rw-r--r-- 1 root root 0 Jul 28 05:43 exec-test.txt

컨테이너 내부에서 현재 위치를 확인하고 문자열을 출력했으며, exec-test.txt 파일을 생성했습니다.
```

4-5)xec로 실행한 셸 종료
```bash
root@d8e2b0e9f85f:/# exit
exit

exit 명령을 사용하여 docker exec로 실행한 Bash 셸을 종료했습니다.
```
4-6) 컨테이너 실행 상태 재확인

% docker ps
```bash
CONTAINER ID   IMAGE     COMMAND            CREATED          STATUS          PORTS     NAMES
d8e2b0e9f85f   ubuntu    "sleep infinity"   49 minutes ago   Up 49 minutes             ubuntu-background

Bash 셸에서 나왔지만 ubuntu-background 컨테이너는 계속 Up 상태로 실행되고 있었습니다.
이는 exit로 종료된 프로세스가 컨테이너의 기본 프로세스인 sleep infinity가 아니라, docker exec로 추가 실행한 bash 프로세스이기 때문입니다.
```

4-7) 컨테이너 로그 확인
```bash
% docker logs ubuntu-background

위 코드를 입력해도 별도의 로그가 출력되지 않았습니다.
컨테이너의 기본 프로세스인 sleep infinity가 표준 출력이나 표준 오류에 내용을 기록하지 않기 때문에 로그가 비어 있는 것이 정상입니다.
```

4-8) 컨테이너 리소스 사용량 확인
```bash
% docker stats ubuntu-background

- 출력결과:
CONTAINER ID   NAME                CPU %     MEM USAGE / LIMIT     MEM %     NET I/O         BLOCK I/O        PIDS
d8e2b0e9f85f   ubuntu-background   0.00%     2.156MiB / 11.74GiB   0.02%     1.13kB / 126B   51.9MB / 4.1kB   1
^C%

docker stats 명령을 사용하여 컨테이너의 CPU, 메모리, 네트워크, 디스크 사용량을 확인했습니다.
출력은 실시간으로 갱신되므로 확인 후 Control + C를 눌러 종료했습니다.
```

4-9) 컨테이너 중지
```bash
% docker stop ubuntu-background
ubuntu-background
docker stop 명령을 사용하여 실행 중인 컨테이너를 정상적으로 중지했습니다.
```
4-10) 실행 중인 컨테이너 재확인
``` bash
% docker ps

실제 출력 결과:
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

중지된 ubuntu-background 컨테이너가 실행 중인 컨테이너 목록에서 사라진 것을 확인했습니다.
```
4-11) 전체 컨테이너 확인
```bash
% docker ps -a

실제 출력 결과
CONTAINER ID   IMAGE         COMMAND            CREATED          STATUS                       PORTS     NAMES
d8e2b0e9f85f   ubuntu        "sleep infinity"   59 minutes ago   Exited (137) 3 minutes ago             ubuntu-background
28c7aae53230   ubuntu        "bash"             2 hours ago      Exited (0) 2 hours ago                 ubuntu-practice
2daaee979015   hello-world   "/hello"           16 hours ago     Exited (0) 16 hours ago                hello-container

docker ps -a 명령에서는 ubuntu-background 컨테이너가 Exited (0) 상태로 표시되었습니다.
```

4-12) docker run, start, exec의 차이
docker run: 이미지를 기반으로 새로운 컨테이너를 생성하고 실행합니다.
docker start: 기존에 생성되어 중지된 컨테이너를 다시 실행합니다.
docker exec: 현재 실행 중인 컨테이너 안에서 추가 명령이나 프로세스를 실행합니다.
docker run -it ubuntu bash로 실행한 컨테이너는 Bash가 기본 프로세스이므로 exit를 입력하면 컨테이너도 중지됩니다.
반면 sleep infinity를 기본 프로세스로 실행한 컨테이너에 docker exec -it로 진입한 경우, exit는 추가로 실행된 Bash만 종료합니다. 따라서 기본 프로세스가 유지되는 동안 컨테이너도 계속 실행됩니다.

4-13)Docker 기본 운영 명령 실습 결과
이번 실습까지 다음 Docker 기본 운영 명령을 수행했습니다.
docker images: 로컬 이미지 목록 확인
docker ps: 실행 중인 컨테이너 확인
docker ps -a: 종료된 컨테이너를 포함한 전체 목록 확인
docker logs: 컨테이너 로그 확인
docker stats: 컨테이너 리소스 사용량 확인
docker stop: 실행 중인 컨테이너 중지
docker start: 중지된 컨테이너 재실행
docker exec: 실행 중인 컨테이너 안에서 추가 명령 실행
이를 통해 Docker 이미지와 컨테이너의 상태를 확인하고 컨테이너를 실행, 재시작, 중지하며 로그와 리소스를 점검하는 기본 관리 방법을 확인했습니다.


### ### 4-5. NGINX 기반 커스텀 이미지 빌드

공식 NGINX 이미지를 베이스로 사용하고, 직접 작성한 정적 웹페이지를 포함한 커스텀 Docker 이미지를 제작했습니다.

#### 프로젝트 디렉토리 생성

```bash
$ mkdir -p ~/codysseysetup/custom-nginx/app
$ cd ~/codysseysetup/custom-nginx
$ pwd
/Users/wonjun/codysseysetup/custom-nginx
```

커스텀 이미지 실습을 위한 디렉토리와 정적 웹페이지를 저장할 `app` 디렉토리를 생성했습니다.

프로젝트 구조는 다음과 같습니다.

```text
custom-nginx/
├── Dockerfile
└── app/
    └── index.html
```

#### 정적 웹페이지 작성

`app/index.html` 파일에 NGINX를 통해 제공할 정적 웹페이지를 작성했습니다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Codyssey Docker Practice</title>
</head>
<body>
  <h1>Codyssey Docker Practice</h1>
  <p>NGINX 기반 커스텀 Docker 이미지가 정상적으로 실행되었습니다.</p>
</body>
</html>
```

파일 내용을 확인했습니다.

```bash
$ cat app/index.html

- 실제 출력 결과:
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Codyssey Docker Practice</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 720px;
      margin: 80px auto;
      padding: 0 24px;
      line-height: 1.6;
    }

    h1 {
      margin-bottom: 12px;
    }

    .status {
      padding: 16px;
      border: 1px solid #cccccc;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <h1>Codyssey Docker Practice</h1>

  <div class="status">
    <p>NGINX 기반 커스텀 Docker 이미지가 정상적으로 실행되었습니다.</p>
    <p>작성자: wonjun</p>
  </div>
</body>
</html>
```

#### Dockerfile 작성

아래 명령어로 Dockerfile을 생성합니다.

```bash
cat > Dockerfile <<'EOF'
```
이후 아래 코드를 작성합니다.

```dockerfile
FROM nginx:alpine

COPY app/index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

작성한 Dockerfile의 각 명령은 다음 목적을 가집니다.

* `FROM nginx:alpine`: 공식 NGINX Alpine 이미지를 베이스 이미지로 사용합니다.
* `COPY`: 직접 작성한 `index.html`을 NGINX 기본 웹 문서 경로에 복사합니다.
* `EXPOSE 80`: 컨테이너의 NGINX 웹 서버가 80번 포트를 사용한다는 정보를 명시합니다.

Alpine 기반 이미지는 일반적인 Linux 이미지보다 크기가 작은 편이므로 간단한 정적 웹 서버 실습에 적합합니다.

#### 프로젝트 파일 확인

```bash
find . -maxdepth 2 -type f

- 출력 결과:
./.DS_Store
./app/index.html
./Dockerfile

빌드에 필요한 Dockerfile과 정적 웹페이지가 올바른 위치에 생성된 것을 확인했습니다.
```

#### 커스텀 이미지 빌드

```bash
docker build -t codyssey-nginx:1.0 .
- 빌드 출력 결과:
=> exporting to image                                                                           0.2s
 => => exporting layers                                                                          0.1s
 => => exporting manifest sha256:bc46906cddb0c8060ede08d60098d83625f09750e8f60af2c19b03cb73700c  0.0s
 => => exporting config sha256:586df5b3a4a114157d597584097a8f6ad05574d605ccf9075fc3fdac5ae2bdb0  0.0s
 => => exporting attestation manifest sha256:d42bdbd37ff07afac3ca70c48b23b65b68672a572fe1223267  0.0s
 => => exporting manifest list sha256:05d607f0918f24516374e698213a481f077126816fe64597b40110709  0.0s
 => => naming to docker.io/library/codyssey-nginx:1.0                                            0.0s
 => => unpacking to docker.io/library/codyssey-nginx:1.0 
```

`docker build` 명령을 사용하여 현재 디렉토리의 Dockerfile을 기반으로 이미지를 빌드했습니다.

* `-t codyssey-nginx:1.0`: 이미지 이름을 `codyssey-nginx`, 태그를 `1.0`으로 지정합니다.
* `.`: 현재 디렉토리를 Docker 빌드 컨텍스트로 지정합니다.

빌드 과정에서 `nginx:alpine` 베이스 이미지를 다운로드하고, 작성한 HTML 파일을 포함한 새로운 이미지가 생성되었습니다.

#### 생성된 이미지 확인

```bash
$ docker images codyssey-nginx
실제 출력 결과를 붙여넣습니다.
```

이미지 목록에서 `codyssey-nginx:1.0`이 생성된 것을 확인했습니다.

#### 커스텀 이미지 기반 컨테이너 실행

```bash
$ docker run -d --name codyssey-web codyssey-nginx:1.0
실제 컨테이너 ID를 붙여넣습니다.
```

직접 빌드한 `codyssey-nginx:1.0` 이미지를 기반으로 `codyssey-web` 컨테이너를 백그라운드에서 실행했습니다.

#### 실행 상태 확인

```bash
$ docker ps
실제 출력 결과를 붙여넣습니다.
```

`codyssey-web` 컨테이너가 `Up` 상태로 실행되는 것을 확인했습니다.

#### 컨테이너 로그 확인

```bash
$ docker logs codyssey-web
실제 출력 결과를 붙여넣습니다.
```

NGINX가 정상적으로 초기화되고 웹 서버 실행 준비가 완료된 것을 로그를 통해 확인했습니다.

#### 컨테이너 내부 HTML 파일 확인

```bash
$ docker exec codyssey-web cat /usr/share/nginx/html/index.html
실제 출력 결과를 붙여넣습니다.
```

컨테이너 내부의 NGINX 웹 문서 경로에서 직접 작성한 HTML 내용이 출력되는 것을 확인했습니다.

이를 통해 Dockerfile의 `COPY` 명령으로 정적 웹페이지가 커스텀 이미지에 정상적으로 포함되었음을 검증했습니다.

#### 컨테이너 정리

```bash
$ docker stop codyssey-web
codyssey-web

$ docker rm codyssey-web
codyssey-web
```

다음 포트 매핑 실습에서 같은 이름으로 컨테이너를 다시 실행하기 위해 기존 컨테이너를 중지하고 삭제했습니다.

컨테이너를 삭제한 후에도 커스텀 이미지는 유지되는 것을 확인했습니다.

```bash
$ docker images codyssey-nginx
실제 출력 결과를 붙여넣습니다.
```

#### 커스텀 이미지 제작 결과

공식 `nginx:alpine` 이미지를 기반으로 직접 작성한 정적 웹페이지를 추가하여 `codyssey-nginx:1.0` 커스텀 이미지를 제작했습니다.

Docker 이미지는 애플리케이션 실행에 필요한 파일과 설정을 포함하는 템플릿이며, 빌드한 이미지로부터 실제 실행 인스턴스인 컨테이너를 생성할 수 있음을 확인했습니다.



### 4-6. 포트 매핑 접속

### 4-7. 바인드 마운트

### 4-8. 볼륨 영속성

### 4-9. Git 설정 및 GitHub 연동


## 5. 트러블슈팅 [문제 → 원인 → 확인 → 해결]

### 1 설치 문제
1) 문제OrbStack 설치 후 " % docker --version " 입력해도
" zsh: command not found: docker "
같이 오류 출력
2) 원인: 아직 OrbStack의 설정을 완료하지 않음
" % open -a OrbStack "
명령어 입력 후 설정완료 후에야 docker 인식
3) 확인: docker 명령어 정상작동 여부확인
" % docker --version
Docker version 29.4.0, build 9d7ad9f "
4) 해결: OrbStack 설정이후 "docker --version"을 입력 후 출력 정상확인


### 2. echo "Hello, Docker!" > hello.txt 진행 안됨

1) 문제: echo "Hello, Docker!" > hello.txt에서 진행 x
2) 원인: echo 다음에 들어가는 문장 작성에서 ""가 끝맺음이 되지 않아 코드가 끝나지 않았다고 판단함
3) 확인: 코드 작성시 ""가 제대로 끝맺음이 되었는지 확인
4) 해결: 다시 확인결과 코드내 "가 제대로 끝맺음이 되지 않았음

### 3. 잘못된 파일 경로 입력
1)문제: 생성한 파일의 내용을 확인하려고 했지만 No such file or directory 오류가 발생
```bash
root@28c7aae53230:/# cat docter-docker-practice/test.txt
cat: docter-docker-practice/test.txt: No such file or directory
```
2)원인: 파일이나 디렉토리가 생성되지 않았거나, 입력한 경로에 오타
3)확인: 앞서 생성한 디렉토리 이름은 docker-practice였지만, 확인 명령에서는 docter-docker-practice라고 잘못 입력
4)해결: 올바른 상대 경로를 사용하여 파일 내용을 다시 확인후 재시도

```bash
root@28c7aae53230:/# cat docker-practice/test.txt Container data
현재 위치가 루트 디렉토리 /이므로 절대 경로를 사용해도 같은 결과를 확인할 수 있습니다.
root@28c7aae53230:/# cat /docker-practice/test.txt
Container data
```

이번 오류를 통해 명령 실행 시 파일 및 디렉토리 이름의 철자와 경로를 정확하게 확인해야 한다는 점을 확인했습니다.