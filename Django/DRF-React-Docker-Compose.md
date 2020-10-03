# DRF-React docker-compose

> ℹ️ Docker-Compose를 이용해 Django와 React 서버 모두 띄우기

<br>

## Project Structure

> https://github.com/mustnot/Django-React-Todo 에 올려져있다.

```bash
├── docker-compose.yml
├── backend
│   ├── Pipfile
│   ├── Pipfile.lock
│   ├── api
│   ├── db.sqlite3
│   ├── dockerfile
│   ├── manage.py
│   └── todo
└── frontend
    ├── README.md
    ├── build
    ├── dockerfile
    ├── node_modules
    ├── package-lock.json
    ├── package.json
    ├── public
    ├── src
    ├── tsconfig.json
    └── yarn.lock
```

전체 디렉토리 구조는 다음과 같이 정리했다.

* `backend` : DRF (Django-REST-Framework) 로 작성된 API가 있는 폴더로 패키지 관리는 `pipenv` 로 관리한다.
* `frontend` : CRA (Create React App) 으로 만들어진 React Todo App 이 있는 폴더로 `node_modules` 폴더와 `package.json`이 있다.

<br>

### Django DockerFile

> ./backend/dockerfile

```dockerfile
FROM python:3.7

RUN pip install pipenv
ENV BACKEND_DIR /app/backend

COPY Pipfile Pipfile.lock ${BACKEND_DIR}/
WORKDIR ${BACKEND_DIR}

RUN pipenv install

EXPOSE 8000
CMD ["pipenv", "run" "start"]
```

<br>

### React DockerFile

> ./frontend/dockerfile

```dockerfile
FROM node:alpine

ENV FRONTEND_DIR /app/frontend

WORKDIR ${FRONTEND_DIR}
COPY . ${FRONTEND_DIR}/

RUN npm install

EXPOSE 3000
CMD ["yarn", "start"]
```

<br>

### Docker-Compose.yml

```dockerfile
version: "3"

services:
  django:
    build: ./backend
    command: ["pipenv", "run", "start"]
    volumes:
      - ./backend:/app/backend
    ports:
      - "8000:8000"

  frontend:
    build: ./frontend
    command: ["yarn", "start"]
    volumes:
      - ./frontend:/app/frontend
      - node_modules:/app/frontend/node_modules
    ports:
      - "3000:3000"
    tty: true

volumes:
  node_modules:
```

<br>

### 명령어

1. `docker-compose build`
2. `docker-compose up`
3. 끝