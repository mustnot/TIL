## Docker Compose란?

> 📌 다중 컨테이너 도커 어플리케이션을 정의하고 실행하기 위한 도구이다.

실습은 docker-compose를 이용하여 node.js와 redis 두 개의 컨테이너를 서로 연결하고 동시에 빌드 및 실행하는 것을 해보려 한다.

<br>

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

## docker-compose 작성하기

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