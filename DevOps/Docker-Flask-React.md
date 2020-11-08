# React-Flask-uWSGI-Nginx with Docker

> 한 대의 서버에 React, Flask, uWSGI, Nginx 모두 구성해야할 필요가 있어 나중에 작업을 위해 정리한 내용이다.

<br>

**Project Structure**

```bash
├── docker-compose.yml
├── api
│   ├── app.py
│   ├── dockerfile
│   ├── requirements.txt
│   └── uwsgi.ini
├── web
│   ├── build
│   ├── dockerfile
│   ├── package.json
│   ├── public
│   ├── src
│   └── yarn.lock
└── nginx
    ├── dockerfile
    └── nginx.conf
```

<br>

**Docker-Compose.yml**

```yaml
version: "3"

services:
  api:
    build: ./api
    container_name: api
    restart: always
    command: ["uwsgi", "uwsgi.ini"]
    volumes:
      - ./api:/api
    ports:
      - "8000"

  web:
    build: ./web
    container_name: web
    restart: always
    command: ["yarn", "start"]
    volumes:
      - ./web:/web
    ports:
      - "3000"

  nginx:
    build: ./nginx
    container_name: nginx
    restart: always
    ports:
      - "80:80"
    volumes:
      - ./nginx:/etc/nginx/conf.d
```

<br>

## Flask

**app.py**

```python
from flask import Flask

app = Flask(__name__)

@app.route("/api/", methods=["GET"])
def index():
    return "hello world"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

**requirements.txt**

```txt
flask==1.1.2
uwsgi==2.0.19.1
```

**uwsgi.ini**

```ini
[uwsgi]
wsgi-file=app.py
callable=app
socket=:8000
processes=2
master=true
vacum=true
chmod-socket=660
```

**dockerfile**

```dockerfile
FROM python:3.9

ENV API_DIR /api/
WORKDIR ${API_DIR}

ADD ./ ${API_DIR}/

RUN pip3 install -r requirements.txt

EXPOSE 8000
CMD ["uwsgi", "uwsgi.ini"]
```

<br>

## React

> ℹ️ create-react-app 명령어로 생성된 간단한 웹이다.

```bash
$ npx create-react-app web
```

**dockerfile**

```dockerfile
FROM node:alpine

ENV WEB_DIR /web
WORKDIR ${WEB_DIR}

ADD ./ ${WEB_DIR}}/

RUN npm install

EXPOSE 3000
CMD ["yarn", "start"]
```

<br>

## Nginx

**nginx.conf**

```nginx
server {
    listen 80;

    location /api {
        include uwsgi_params;
        uwsgi_pass api:8000;
    }

    location / {
        proxy_http_version 1.1; 
        proxy_set_header    Host $host;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto $scheme;
        proxy_set_header    Upgrade $http_upgrade;
        proxy_set_header    Connection "upgrade"; 
        proxy_pass http://web:3000;
        proxy_read_timeout  120;
    }
}
```

**dockerfile**

```dockerfile
FROM nginx

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/conf.d/
```

<br>

<br>

## Conclusion

```bash
$ docker-compose build
$ docker-compose up -d
```

