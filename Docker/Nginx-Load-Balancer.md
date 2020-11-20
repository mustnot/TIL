# Flask, Nginx Load Balancer

> Nginx Load Balancing 기능을 이용하여 무중단 배포 방법에 대해 공부

<br>

## Project Structure

```bash
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

<br>

## Flask

Flask App은 아주 간단하게 코드의 변경사항만 확인할 수 있게, `index()` 에 `hello_world_v1` 과 같이 버전 명만 보이게 작성했다.

**run.py**

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

```ini
[uwsgi]
uid=root
gid=root

chdir=/app/flask
callable=app
wsgi-file=run.py
lazy-apps=true

http=:5000
http-websockets=true

master=true
processes=4
enable-threads=true

chmod-socket=666
buffer-size=32768
```

* `http=:5000`
* `http-websockets=true`



<br>

**Dockerfile**

```dockerfile
FROM python:3.8

ENV APP_DIR /app/flask

WORKDIR ${APP_DIR}

ADD requirements.txt ${APP_DIR}/
RUN pip install -r requirements.txt

EXPOSE 5000
COPY . ${APP_DIR}/
CMD ["uwsgi", "uwsgi.ini"]
```

* `FROM python:3.8`  : `slim` 버전의 파이썬을 사용할 수도 있었지만, 그럴 경우 `uwsgi`가 정상적으로 설치되지 않는 문제가 있다. `C Compile` 문제라고 나오며, 해결 방법이 있겠지만 그건 나중에...
* `ENV APP_DIR /app/flask` : 경로를 환경변수 설정으로 관리
* `ADD requirements.txt` : `COPY . .` 로 한번에 가져와도 되지만 분리시킨 이유는 Cache를 적극적으로 이용하기 위함인데, `requirements.txt` 에는 변경사항이 없음에도 불구하고 모든 파일을 다 가져오게 되면 새로 캐싱되는 불편함이 있다. (때로 매번 불필요한 설치과정이 계속될 수 있다)
* `CMD ["uwsgi", "uwsgi.ini"]` : uwsgi 실행 명령어이다.















