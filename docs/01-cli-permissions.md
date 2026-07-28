# CLI와 파일 권한 실습

[← README로 돌아가기](../README.md)

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

3) 프로젝트로 이동한 뒤 실습용 디렉토리 생성 및 이동

```bash
cd /Users/wonjun/Developer/codyssey/codysseysetup
mkdir -p cli-practice
cd cli-practice
```

결과:
% pwd
/Users/wonjun/Developer/codyssey/codysseysetup/cli-practice

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

#### 절대 경로와 상대 경로

절대 경로는 `/Users/wonjun/Developer/codyssey/codysseysetup`처럼 파일 시스템의 시작점부터 위치를 모두 표현하므로 현재 작업 위치와 관계없이 같은 대상을 가리킵니다. 상대 경로는 `custom-nginx/app/index.html`처럼 현재 위치를 기준으로 대상을 찾습니다.

재현 명령에서는 먼저 `cd`로 프로젝트 루트를 명확히 지정한 뒤 프로젝트 내부 파일에는 상대 경로를 사용합니다. Docker 바인드 마운트처럼 호스트의 정확한 위치가 필요한 경우에는 `$(pwd)/custom-nginx/bind-app`처럼 절대 경로로 확장하여 사용합니다. 컨테이너 내부의 `/usr/share/nginx/html`과 `/data`도 실행 위치에 영향을 받지 않도록 절대 경로를 사용합니다.

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
