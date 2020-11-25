## Docker-Dockerfile

> Docker에서 Dockerfile을 이용하는 방법에 대해 공부했다.

<br>

### Docker 이미지 생성 순서

1. Dockerfile 작성 : Docker image를 만들기 위한 설정 파일로 컨테이너 동작에 대한 설정을 정의함
2. 도커 클라이언트 : Dockerfile에 입력된 것들이 도커 클라이언트에 전달된다.
3. 도커 서버 : 도커 클라이언트에 전달된 모든 작업을 진행하는 서버
4. 이미지 생성

<br>

### Dockerfile이란?

도커 이미지를 만들기 위한 설정 파일로 컨테이너가 어떻게 동작해야 하는지에 대한 설정을 정의하는 파일이다.

<br>

#### 순서

1. Base Image를 명시한다. (ex. ubuntu:16.04)
   * 베이스 이미지란? 도커 이미지는 여러 개의 레이어로 구성되어 있는데, 그 중 베이스 이미지는 기반이 되는 이미지를 의미한다. 레이어는 중간 단계의 이미지라고 생각하면 된다.

2. 추가적인 파일을 위한 명령어를 나열한다.
3. 컨테이너 시작시 실행 될 명령어를 명시한다.

<br>

1. FROM : 이미지 생성시 기반이 되는 이미지 레이어로 <이미지 이름>:<태그> 형식으로 작성한다.
   (만약 태그가 없는 경우 latest 가장 최신 버전으로 지정된다)
2. RUN : 명령어를 입력할 수 있고 도커 이미지가 생성되기 전에 수행할 쉘 명령어를 작성한다.
3. CMD : 컨테이너가 시작되었을 때 실행할 파일 또는 쉘 스크립트로 "1회"만 작성할 수 있다.

<br>

**예시 :** Hello 호출하기

```dockerfile
FROM alpine

CMD ["echo", "Hello"]
```

<br>

### Dockerfile로 Docker image 만들기

1. `docker build ./` 또는 `docker build .`

```bash
$ docker build .
Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM alpine
latest: Pulling from library/alpine
df20fa9351a1: Pull complete
Digest: sha256:185518070891758909c9f839cf4ca393ee977ac378609f700f60a771a2dfe321
Status: Downloaded newer image for alpine:latest
 ---> a24bb4013296
Step 2/2 : CMD ["echo", "hello"]
 ---> Running in 7a1643d64f70
Removing intermediate container 7a1643d64f70
 ---> 0022caca0956
Successfully built 0022caca0956

$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED
<none>              <none>              0022caca0956        17 seconds ago     
alpine              latest              a24bb4013296        4 months ago       
```

(ℹ️`docker images`에서 나온 가장 맨 위에 나타난 `0022caca0956` 이 방금 생성한 도커 이미지이다)

2. `docker run [IMAGE ID]`

```bash
$ docker run 0022caca0956
Hello
```

<br>

<br>

## Dockerizing a Node.js Web App

> 📌 [Dockerizing a Node.js Web app - Node.js docs](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/) 참고

<br>

### Node.js 앱 만들기 순서

1. `package.json` 만들기 : 프로젝트의 정보와 프로젝트에서 사용 중인 패키지의 의존성을 관리
2. `server.js` 만들기 : 프로젝트의 시작점(Entry Point)로서 가장 먼저 시작되는 파일 (꼭 `server.js` 일 필요는 없다)

<br>

**package.json**

```json
{
  "name": "nodejs-docker-app",
  "version": "1.0.0",
  "description": "Node.js on Docker",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "keywords": [],
  "author": "",
   "dependencies": {
    "express": "^4.16.1"    
  }
}
```

* `express` : Javascript와 jQuery의 관계처럼 Node.js의 API를 단순화하고 새로운 기능을 추가하여 Node.js를 더 쉽고 유용하게 사용할 수 있게 해준다.

<br>

**server.js**

```javascript
const { response } = require('express');
const express = require('express');

// Constants
const PORT = 8080;
const HOST = "0.0.0.0";

// App
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);
```

<br>

### Dockerfile 작성하기

**dockerfile**

```dockerfile
FROM node:10

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

COPY . .
EXPOSE 8080
CMD ["node", "server.js"]
```

* `WORKDIR /usr/src/app` : `/usr/src/app` 폴더로 디렉토리 이동 (cd와 동일)

* `COPY package*.json ./` : `package.json` / `package-lock.json` 등을 현재 디렉토리 `./` 에 복사시킨다.

* `EXPOSE 8080` : `server.js`에 작성했던 Port가 8080이기 때문에 8080으로 작성한다.

<br>

**.dockerignore**

```dockerfile
node_modules
npm-debug.log
```

* `COPY . .` 명령어로 인해 디렉토리 내 모든 파일이 컨테이너 내부로 이동하는데, 만약 이미 `npm install` 명령어를 통해 의존성 라이브러리를 모두 설치했다면 그 용량이 꽤나 크기 때문에 docker container 만들기 전에 무시하도록 설정한다. 

<br>

### 배포 이후에 소스 변경이 필요한 경우 꼭 다시 빌드해야하는가?

> 📌 이 부분은 항상 궁금했던 부분이였다. (지금까지 미련하게 매번 빌드했..)

#### Volume을 사용하는 방법

COPY의 경우 이미지가 생성되면서 컨테이너 내부로 파일을 복사하기 때문에, 소스코드가 변경되면 실시간으로 반영되는 것이 아니라 로컬과 컨테이너가 독립적으로 분리되어 있기 때문에 매번 재배포하는 불편함이 있다.

하지만 Volume을 사용하면, COPY와 달리 로컬에 있는 파일을 참조하여 실행하도록 한다.

**명령어**

* `-d` : detached mode로 백그라운드 모드와 동일

```bash
$ docker run -d -p 8080:8080 -v /usr/src/app/node_modules -v $(pwd):/usr/src/app <IMAGE ID>
```

이 방법을 사용하면, 다음과 같은 방법으로 소스가 변경되더라도 재실행이 가능하다.

1. `docker stop <CONTAINER>` -> `docker run`
2. `docker restart <CONTAINER>`

<br>

## 도커 컨테이너의 생명 주기

![CE9BBFBE-7F12-4B1C-B344-262C1080264A](https://user-images.githubusercontent.com/52126612/94931097-4c1d6380-0502-11eb-99f0-03774eb2b9c1.png)

<br>

### 이미지로 컨테이너 만들기

> ℹ️ 이미지는 응용 프로그램을 실행하는데 필요한 **<u>모든 것</u>**을 포함하고 있다.

1. 컨테이너가 시작 될 때 실행되는 명령어 ex) run [프로그램]
2. 파일 스냅샷 ex) 컨테이너에 실행될 [프로그램]의 파일 혹은 스냅샷
   ( * 파일 스냅샷은 디렉토리나 파일을 카피한 것으로 이해하면 쉽다. )

<br>

### docker stop vs kill

두 명령어 모두 기본적으로 실행중인 컨테이너를 중지시킨다는 공통점을 가지고 있다.

* `docker stop [CONTAINER ID or NAME]` : Gracefully 하게 컨테이너를 중지시켜, 그 동안 하고 있던 작업들을 모두 완료하고 컨테이너를 중지시킨다.
* `docker kill [CONTAINER ID or NAME]` : Stop과 달리 바로 컨테이너를 중지시킨다.

<br>

### 실행중인 컨테이너에 명령어 전달

* `docker exec [CONTAINER ID or NAME] [COMMAND]` : 실행 중인 컨테이너에 명령어 전달 가능
  * `docker run [COMMAND]` 와 차이점은 이미 실행 중인 컨테이너에 명령어 전달이 가능하다는 점

<br>

### 레디스를 이용한 컨테이너 이해

1. **레디스 서버**(redis) 작동
   * `docker run redis`
2. **레디스 클라이언트**(redis-cli) 실행 후 명령어를 **레디스 서버**에 전달
   * `docker exec -it [REDIS CONTAINER ID] redis-cli`
3. 레디스 클라이언트에 키-밸류로 데이터 넣고, 가져와보기
   * `set key1 hello`
   * `get key1`