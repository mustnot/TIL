# Flask Nginx Load-Balancing

> Nginx 설정 중 하나인 Upstream Backup 기능을 이용한 무중단 배포 방법

### Project Structure

```bash
.
├── docker-compose.yml
├── flask_app
│   ├── Dockerfile
│   ├── deploy.sh
│   ├── requirements.txt
│   ├── run.py
│   └── uwsgi.ini
└── nginx
    ├── Dockerfile
    └── nginx.conf
```

전체적인 구성은 다음과 같다. `deploy.sh`는 쉽게 배포할 수 있게 빌드와 실행 명령어를 간략하게 작성해놓은 스크립트이다.

<br>

## Flask App

### run.py

Flask App은 간단하게 코드의 변경사항만 확인할 수 있도록 `hello_world_v1`과 같이  버전명만 리턴

```python
from flask import Flask

app = Flask(__name__)

@app.route("/", methods=["GET"])
def index():
    return "hello_world_v1"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

<br>

### Dockerfile

`python 3.9`의 `slim` 버전을 이용하여 빌드하도록 작성

```dockerfile
FROM python:3.9-slim

WORKDIR /app/flask
ADD requirements.txt ./
RUN pip install -r requirements.txt

EXPOSE 5000
COPY . .
CMD ["python", "run.py"]
```

<br>

## Nginx

### nginx.conf

```nginx
events { worker_connections 1024; }

http {
    upstream flask-app {
        server YOUR_IP_ADDRESS:8000;
        server YOUR_IP_ADDRESS:8001 backup;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://flask-app;
            proxy_http_version  1.1; 
            proxy_set_header    Host $host;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $remote_addr;
            proxy_set_header    X-Forwarded-Proto $scheme;
            proxy_set_header    Upgrade $http_upgrade;
            proxy_set_header    Connection "upgrade"; 
        }
    }
}
```

지금까지 오픈 소스인 HAProxy를 이용하여 로드 밸런서를 구현했는데, Nginx 설정을 이용해서도 로드 밸런싱 기능을 사용할 수 있다는 사실을 처음 알았다.

```nginx
upstream flask-app {
	server YOUR_IP_ADDRESS:8000;
  server YOUR_IP_ADDRESS:8001 backup;
}
```

사실 로드 밸런싱이라기보다 Backup 기능을 이용한 무중단 배포 방법에 대해 작성한 내용이다. 요약하면, 8000, 8001번 포트에 `Version 1` 상태의 웹이 올라가 있는 상태에서 8000번 포트에 앱을 종료하고 `Version 2` 의 웹을 올려 만약 `Version 2` 에 문제가 생기더라도 8001번 포트에 올라가있는 `Version 1` 이 그 일을 대신하면서 계속해서 서비스를 중단 없이 이어나갈 수 있는 방법이다. (사실 이 방법이 정확한 방법인지는 다소 자신감이 없다)

<br>

**Dockerfile**

```dockerfile
FROM nginx
COPY nginx.conf /etc/nginx/nginx.conf
```

**docker-compose.yml**

```yaml
version: "3"

services:
  nginx:
    build: ./nginx
    container_name: nginx
    restart: always
    ports:
      - "80:80"
    volumes:
      - /etc/localtime:/etc/localtime:ro
    environment:
      - TZ=Asia/Seoul
```

<br>

## 실행 과정

```bash
$ docker-compose up -d --build
```

`docker-compose.yml`에 작성된 설정 그대로 `nginx` 먼저 실행되도록 작성했다. 이 상태로 `http://localhost:80`에 접속하면 `502 Bad Gateway` 에러가 발생한다.

### deploy.sh

```sh
#!/bin/bash
version="$1"
port="$2"

echo "Start Deploy Version: $version Port: $port"

docker build -t flask-$version ./

docker rm -f `docker ps -a | grep flask-$port | awk '{print $1}'`

docker run -d \
-p $port:5000 \
--name flask-$port \
--restart always \
flask-$version

echo "Done!"

docker logs -f flask-$port
```

```bash
$ cd flask_app
$ ./deploy.sh 1.0 8000
$ ./deploy.sh 1.0 8001
```

첫 번째 두 번째 인수로 각각 버전과 포트를 입력하면 알아서 빌드와 실행까지 완료되도록 스크립트를 작성하여 한번에 실행하였다.

**실행 결과**

```bash
$ docker ps
IMAGE               COMMAND                  PORTS                    NAMES
flask-1.0           "python run.py"          0.0.0.0:8001->5000/tcp   flask-8001
flask-1.0           "python run.py"          0.0.0.0:8000->5000/tcp   flask-8000
new-deploy_nginx    "/docker-entrypoint.…"   0.0.0.0:80->80/tcp       nginx
```

`nginx`와 `flask_app` 2개가 모두 정상 실행 되었다. 이 상태에서 8000번 포트에 실행 중인 `flask-1.0`을 삭제하고 `run.py`를 수정 후 다시 8000번으로 배포하면 중단 없이 배포가 완료된다. 그 사이에 빈 시간 동안에는 backup에 설정된 8001번 포트에 실행 중인 `flask-1.0`이 대신한다.

```bash
$ docker rm -f flask-8000
# rm -f 는 좋은 명령어는 아니다. 참고만 하자
$ ./deploy.sh 2.0 8000
```

정상적으로 배포가 완료되었다. 만약에 2.0 빌드 중에 문제가 생겼거나 혹은 정상적으로 실행이 되지 않는다면 위에서 언급한 바와 같이 `flask-1.0`이 대신한다.

```bash
$ docker rm -f flask-8000
```

이렇게 8000번 포트에 실행 중인 앱을 제거하더라도 정상적으로 실행된다.







