## Docker Compose란?

> 📌 다중 컨테이너 도커 어플리케이션을 정의하고 실행하기 위한 도구이다.

실습은 docker-compose를 이용하여 node.js와 redis 두 개의 컨테이너를 서로 연결하고 동시에 빌드 및 실행하는 것을 해보려 한다.

<br>

## Node.js App & React

### Node.js App

**server.js**

```javascript
const { response } = require('express');
const express = require("express");
const redis = require("redis")

// Constants
const PORT = 8080;
const HOST = "0.0.0.0";

// App
const app = express();

const redis_client = redis.createClient({
  host: "redis-server",
  port: 6379
})

redis_client.set("number", 0)

app.get('/', (req, res) => {
  redis_client("number", (err, number) => {
    redis_client.set("number", parseInt(number) + 1)
    res.send(`number is increase 1! now: ${number}`)
  })
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);
```

<br>

**dockerfile**

```dockerfile
FROM node:10

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm install

EXPOSE 8080
CMD ["node", "server.js"]
```

<br>

### docker-compose 작성하기

> docker-compose는 확장자가 yml인 파일이다. 

<br>

**docker-compose.yml**

```yaml
version: "3"

services: 
    redis-server:
        image: "redis"
    
    node-app:
        build: .
        command: ["node", "server.js"]
        volumes: 
            - ./:/usr/src/app
            - node_modules:/usr/src/app/node_modules
        ports:
            - "8080:8080"
        tty: true

volumes:
    node_modules:
```

* `version: "3"` : yaml 파일 포맷의 버전을 입력하며, 버전 3이 현재 호환성이 가장 좋다(?)
* `services` : docker-compose로 연결할 서비스 목록과 설정을 입력하는 곳이다. (명칭은 자유롭게 써도 된다.)
  * `redis-server` : redis-server는 단순히 image만 입력하였다. redis도 6379라는 port로 통신하는데, 입력되지 않은 것을 보면 내부에서만 작동하고 컨테이너 외부와는 통신이 필요없기 때문으로 보인다.
  * `node-app` : 위에서 작성한 node.js app의 설정이 들어가는 곳으로, 이전에 `docker run ...` 했던 것과 유사하게 작성하면 된다.
* `volumes` : 이 것이 무엇인지는 나중에 봐야할 것 같다. 보기에는 `services`에서 작성한 `volumes`와 다른 역할을 하는 것으로 보이는데, 검색해봐도 정확히 판단되지가 않는다.

<br>

<br>

## React App

`npx create-react-app ./` 으로 생성된 가장 기초적인 React App을 도커로 실행해본다.

<br>

### dockerfile을 이용한 방법

> dockerfile 역시 개발 단계, 개발/운영/테스트 단계에 맞춰 도커 파일을 여러 개 작성할 수 있다.

**dockerfile.dev**

```dockerfile
FROM node:alpine

WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install

COPY ./ ./

EXPOSE 3000
CMD ["npm", "run", "start"]
```

**build**

* `docker build -f dockerfile.dev ./`
  * 기존 `docker build ./`와 다른 점은 `-f` 옵션을 두어 도커 파일을 지정해두었는데, 기본적으로 `dockerfile`을 참조하여 빌드하기 때문에 임의로 지정해주어야한다.
* `docker run -it -p 3000:3000 [IMAGE ID]`
  * react app은 default로 3000번 포트를 사용한다.

<br>

## docker-compose를 이용한 방법

**docker-compose.yml**

```yaml
version: "3"

services:
  react:
    build:
      context: .
      dockerfile: dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - /usr/src/app/node_modules
      - ./:/usr/src/app
    stdin_open: true
    tty: true

```

* `build`
  * context : 도커 이미지를 구성하기 위한 파일과 폴더들이 있는 위치 (기존에는  `build: ./` 만 입력했던 것과 다르다.)
  * dockerfile : -f 옵션에서 두었던 build 하기위한 dockerfile명을 작성한다.

* `stdin_open` : 리액트 앱을 끌 때 필요하다.

