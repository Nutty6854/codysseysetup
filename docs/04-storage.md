# 바인드 마운트와 Docker 볼륨

[← README로 돌아가기](../README.md)

### 4-7. 바인드 마운트

바인드 마운트는 호스트의 디렉토리를 컨테이너 경로에 직접 연결합니다. 호스트 파일을 수정했을 때 이미지 재빌드나 컨테이너 재시작 없이 NGINX 응답이 바뀌는지 확인했습니다.

프로젝트 루트에서 변경 전 HTML을 준비했습니다. 다음은 같은 파일을 재현할 수 있는 명령입니다.

```bash
mkdir -p custom-nginx/bind-app
printf '<!DOCTYPE html>\n<html lang="ko">\n<head>\n  <meta charset="UTF-8">\n  <meta name="viewport" content="width=device-width, initial-scale=1.0">\n  <title>Bind Mount Practice</title>\n</head>\n<body>\n  <h1>바인드 마운트 변경 전</h1>\n</body>\n</html>\n' > custom-nginx/bind-app/index.html
```

호스트의 `bind-app` 디렉토리를 NGINX 문서 경로에 읽기 전용으로 연결하고 8081번 포트로 실행했습니다. `$(pwd)`를 사용하면 사용자마다 다른 프로젝트 절대 경로를 현재 위치에서 생성할 수 있습니다.

```bash
docker run -d --name codyssey-bind -p 8081:80 \
  --mount type=bind,source="$(pwd)/custom-nginx/bind-app",target=/usr/share/nginx/html,readonly \
  nginx:alpine
8181c17fd4dfb24179ed1e0277e908f9389edcf14aa2045348511aff7a9fdc9f

docker ps --filter name=codyssey-bind --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
NAMES           STATUS         PORTS
codyssey-bind   Up 9 seconds   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp
```

변경 전 응답을 확인했습니다.

```bash
curl -sS http://localhost:8081

<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bind Mount Practice</title>
</head>
<body>
  <h1>바인드 마운트 변경 전</h1>
</body>
</html>
```

컨테이너를 재시작하지 않고 호스트 파일의 제목을 변경한 뒤 다시 요청했습니다.

```bash
printf '<!DOCTYPE html>\n<html lang="ko">\n<head>\n  <meta charset="UTF-8">\n  <meta name="viewport" content="width=device-width, initial-scale=1.0">\n  <title>Bind Mount Practice</title>\n</head>\n<body>\n  <h1>바인드 마운트 변경 반영 완료</h1>\n</body>\n</html>\n' > custom-nginx/bind-app/index.html

curl -sS http://localhost:8081
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bind Mount Practice</title>
</head>
<body>
  <h1>바인드 마운트 변경 반영 완료</h1>
</body>
</html>
```

마운트 유형과 읽기 전용 설정도 확인했습니다.

```bash
docker inspect codyssey-bind --format 'Type={{(index .Mounts 0).Type}} Source={{(index .Mounts 0).Source}} Destination={{(index .Mounts 0).Destination}} RW={{(index .Mounts 0).RW}}'
Type=bind Source=/Users/wonjun/Developer/codyssey/codysseysetup/custom-nginx/bind-app Destination=/usr/share/nginx/html RW=false
```

`Type=bind`는 호스트 디렉토리가 직접 연결되었음을, `RW=false`는 컨테이너에서 호스트 파일을 수정할 수 없는 읽기 전용 마운트임을 의미합니다. 호스트 변경이 즉시 응답에 반영되어 개발 중 정적 파일을 빠르게 확인할 수 있었습니다.

### 4-8. 볼륨 영속성

Docker 볼륨은 Docker가 관리하는 저장 공간입니다. 데이터를 기록한 첫 번째 컨테이너를 삭제한 뒤, 같은 볼륨을 새 컨테이너에 연결하여 파일이 유지되는지 확인했습니다.

```bash
$ docker volume create codyssey-data
codyssey-data

$ docker run -d --name codyssey-volume-before \
  --mount source=codyssey-data,target=/data \
  ubuntu sleep infinity
be031bd19b4d65b0b2f36262ba69d6e838cb6cf094d530a21f8516a3ccccddd4

$ docker exec codyssey-volume-before \
  sh -c 'echo "컨테이너 삭제 후에도 유지되는 데이터" > /data/hello.txt'

$ docker exec codyssey-volume-before cat /data/hello.txt
컨테이너 삭제 후에도 유지되는 데이터
```

파일을 만든 컨테이너를 삭제했습니다.

```bash
$ docker rm -f codyssey-volume-before
codyssey-volume-before

$ docker ps -a --filter name=codyssey-volume --format 'table {{.Names}}\t{{.Status}}'
NAMES     STATUS
```

첫 번째 컨테이너가 삭제된 상태에서 같은 볼륨을 새 컨테이너에 연결했습니다.

```bash
$ docker run --rm --name codyssey-volume-after \
  --mount source=codyssey-data,target=/data \
  ubuntu cat /data/hello.txt
컨테이너 삭제 후에도 유지되는 데이터

$ docker volume inspect codyssey-data \
  --format 'Name={{.Name}} Driver={{.Driver}} Mountpoint={{.Mountpoint}}'
Name=codyssey-data Driver=local Mountpoint=/var/lib/docker/volumes/codyssey-data/_data
```

첫 번째 컨테이너를 삭제한 이후에도 새 컨테이너에서 같은 파일을 읽었으므로 데이터가 컨테이너의 쓰기 계층이 아니라 `codyssey-data` 볼륨에 영속적으로 저장되었음을 확인했습니다.

#### 바인드 마운트와 볼륨 선택 기준

| 방식 | 저장 위치와 특징 | 적합한 사용 사례 |
| --- | --- | --- |
| 바인드 마운트 | 호스트 경로를 직접 연결하며 호스트 변경이 즉시 반영됨 | 소스코드·설정 파일을 편집하는 개발 환경 |
| Docker 볼륨 | Docker가 저장 위치를 관리하며 컨테이너 삭제와 분리됨 | 데이터베이스·업로드 파일 등 영속 데이터 |

중요 데이터는 볼륨만 믿지 않고 별도로 백업해야 합니다. 다음은 볼륨 내용을 프로젝트의 `backups/` 디렉토리에 tar 파일로 저장하는 예시입니다.

```bash
mkdir -p backups
docker run --rm \
  --mount source=codyssey-data,target=/data,readonly \
  --mount type=bind,source="$(pwd)/backups",target=/backup \
  ubuntu tar -czf /backup/codyssey-data.tar.gz -C /data .
```
