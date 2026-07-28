# 트러블슈팅

[← README로 돌아가기](../README.md)

각 사례를 문제 → 원인 가설 → 확인 → 해결 순서로 기록했습니다.

### 1. OrbStack 설치 후 Docker 명령을 찾지 못함

1) 문제: OrbStack 설치 직후 Docker 버전을 확인했지만 명령을 찾지 못했습니다.

```bash
$ docker --version
zsh: command not found: docker
```

2) 원인 가설: OrbStack 애플리케이션의 최초 설정이 끝나지 않아 Docker CLI와 엔진이 준비되지 않았다고 판단했습니다.

3) 확인 및 해결: OrbStack을 실행해 최초 설정을 완료한 뒤 다시 버전을 확인했습니다.

```bash
$ open -a OrbStack
$ docker --version
Docker version 29.4.0, build 9d7ad9f
```

Docker 버전이 정상적으로 출력되어 CLI를 사용할 수 있음을 확인했습니다.


### 2. echo "Hello, Docker!" > hello.txt 진행 안됨

1) 문제: `echo` 명령을 입력한 뒤 셸이 명령을 실행하지 않고 추가 입력을 기다렸습니다.

2) 원인 가설: 문자열을 감싸는 큰따옴표가 닫히지 않아 zsh가 명령이 아직 끝나지 않았다고 판단한 것으로 보았습니다.

3) 확인: 입력한 명령의 시작과 끝에 큰따옴표가 한 쌍으로 있는지 확인했습니다.

4) 해결: 진행 중인 입력을 `Control + C`로 취소하고 큰따옴표를 올바르게 닫아 다시 실행했습니다.

```bash
$ echo "Hello, Docker!" > hello.txt
$ cat hello.txt
Hello, Docker!
```

### 3. 잘못된 파일 경로 입력
1) 문제: 생성한 파일의 내용을 확인하려고 했지만 `No such file or directory` 오류가 발생했습니다.

```bash
root@28c7aae53230:/# cat docter-docker-practice/test.txt
cat: docter-docker-practice/test.txt: No such file or directory
```

2) 원인 가설: 파일이나 디렉토리가 생성되지 않았거나 입력한 경로에 오타가 있다고 판단했습니다.

3) 확인: 앞서 생성한 디렉토리는 `docker-practice`였지만 확인 명령에는 `docter-docker-practice`라고 입력된 것을 발견했습니다.

4) 해결: 올바른 상대 경로와 절대 경로로 각각 파일을 확인했습니다.

```bash
root@28c7aae53230:/# cat docker-practice/test.txt
Container data

root@28c7aae53230:/# cat /docker-practice/test.txt
Container data
```

현재 위치가 루트 디렉토리 `/`이므로 두 경로가 같은 파일을 가리켰습니다. 이번 오류를 통해 명령 실행 전에 파일명과 현재 작업 경로를 함께 확인해야 한다는 점을 배웠습니다.

### 4. 상위 폴더가 Git 저장소로 잘못 인식됨

1) 문제: 프로젝트 폴더에서 `git status`를 실행했지만 상위 `Developer` 폴더 전체가 저장소 범위로 표시되었습니다.

2) 원인 가설: 프로젝트가 아닌 상위 폴더에서 실수로 `git init`이 실행되어 `/Users/wonjun/Developer/.git`이 생성되었을 가능성을 확인했습니다.

3) 확인:

```bash
$ git rev-parse --show-toplevel
/Users/wonjun/Developer

$ git remote -v
# 출력 없음
```

상위 저장소에는 커밋과 원격 저장소 설정도 없었습니다.

4) 해결: 잘못 생성된 `.git`을 삭제하지 않고 상위 경로에 백업한 뒤, 실제 프로젝트 폴더를 저장소로 초기화하고 GitHub 원격 저장소에 연결했습니다.

```bash
$ cd /Users/wonjun/Developer
$ mv .git ../Developer.git.accidental-backup-20260728
$ cd /Users/wonjun/Developer/codyssey/codysseysetup
$ git init -b main
$ git remote add origin https://github.com/Nutty6854/codysseysetup.git
$ git fetch origin
$ git reset --mixed origin/main
```

복구 후 저장소 루트와 원격 주소를 다시 확인했습니다.

```bash
$ git rev-parse --show-toplevel
/Users/wonjun/Developer/codyssey/codysseysetup

$ git remote -v
origin  https://github.com/Nutty6854/codysseysetup.git (fetch)
origin  https://github.com/Nutty6854/codysseysetup.git (push)
```
