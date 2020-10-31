# Docker Network 

> 📌 Docker-Compose로 Flask와 MySQL을 사용하여 환경을 구축하는데, docker-compose를 이용해 만들어진 환경과 분리되어 dockerfile로 실행된 컨테이너와 서로 네트워크가 분리되어 연결되지 않는 현상을 해결하기 위해 알아보던 중에 알게된 docker network에 대해 간단하게 정리

우선 다음 명령어를 사용해보자.

```bash
$ docker network ls
NETWORK ID          NAME                      DRIVER              SCOPE
212409c3f5e7        bridge                    bridge              local
4d3d8e0c274f        host                      host                local
e762ad38ac7f        none                      null                local
```

 `docker network ls` 명령어를 통해 도커에서 현재 컨테이너가 동작 중인 네트워크 리스트를 볼 수 있다. 내용을 간단히 살펴보자.

* `DRIVER` : 사용 중인 네트워크 드라이버로 자세히 설명하기엔 다른 좋은 글이 있으니 참고하고, 여기서는 `bridge`와 `host` 두 가지만 간단히 살피고 본다.

  * `bridge` : bridge는 사전적인 의미로 "다리"를 의미하는데, 그 의미와 동일하게 생각하면 좋을 것 같은데 docker 이전에 virtualbox를 이용하여 가상환경을 만들 때를 생각하면 처음 이미지를 이용하여 서버를 생성하면 외부로 인터넷 연결이 안되고 이를 해결하기 위해 브리지를 통해 외부와 통신하는데 이와 동일한 역할을 한다고 보면 된다.

  * `host` : host의 네트워크 환경을 그대로 사용할 수 있다는 의미이다.

<br>

**docker-compose.yml**

```yaml
version: "3"

services:
  db:
    image: mysql:5.7
    container_name: mysql
    restart: always
    ports:
      - "3308:3306"
    volumes:
      - ./db/data:/var/lib/mysql
      - ./db/config:/etc/mysql/conf.d
    environment:
      - MYSQL_ALLOW_EMPTY_PASSWORD=true
      - MYSQL_USER=test_user
      - MYSQL_PASSWORD=test_password
      - MYSQL_DATABASE=test_db
```

개발 환경을 구성하기 위해 `docker-compose.yml`에 mysql, flask, redis 등 다양한 여러 어플리케이션들을 `docker-compose`로 묶어서 한번에 올리고 배포하며 테스트했는데, 그러다보니 문제점 중 하나가 컨테이너 중에 하나를 다시 빌드해야할 필요가 있을 경우 모든 서비스를 다시 빌드하는 번거로운 과정을 거쳤고, 시간도 많이 소요되어 docker-compose로만 올릴 서비스와 자주 변경되고 배포되는 환경을 분리하여 컨테이너를 실행시켰는데, 이런 경우 네트워크가 서로 다르게 구성되어 있다보니 연결이 되지 않는 문제가 생겼고 이를 해결하기 위해 `docker network`라는 것을 알게 되었다.

`docker-compose up --build` 를 이용해 서비스를 올리게 되면 설정 내 서비스들은 동일한 네트워크로 연결되어 서로 상호적으로 통신이 원할하게 이뤄지지만, `docker-compose` 외 다른 폴더에서 빌드되어 실행된 컨테이너의 경우 docker-compose와 네트워크가 분리되어 있어 연결이 안되는 경우가 있어 아래와 같은 과정을 거치면 같은 네트워크에서 실행이 가능하다.

```bash
$ docker-compose up -d
$ docker network ls
NETWORK ID          NAME                      DRIVER              SCOPE
212409c3f5e7        bridge                    bridge              local
c59c44885c27        docker-compose_default    bridge              local
4d3d8e0c274f        host                      host                local
e762ad38ac7f        none                      null                local
```

먼저 `docker-compose up -d` 명령어로 컨테이너를 실행시키면, 위와 같이 `[DIR_NAME_default]` 형태의 네트워크 이름이 생성된다. 네트워크 이름은 `docker-compose.yml` 에서 `networks`로 작성하면 된다.

예:

```yaml
networks:
	custom-network-name:
```

이 다음 다른 폴더에서 `dockerfile`을 통해 build하고 실행 시킬 때 다음과 같은 명령어로 실행하면, 동일한 네트워크에 구성한 것과 같은 효과를 얻을 수 있다.

```bash
$ docker build . -t test_web
$ docker run -d -p 5000:5000 --network docker-compose_default --name test_web test_web
```

<br>

<br>

### 명령어 정리

```bash
$ docker run -it --network [NETWORK ID/NAME] --name test alpine /bin/bash
```

* `docker run --network` 명령어를 이용하면, `docker network ls` 명령어에서 나오는 네트워크 중에 선택하여 연결할 수 있다.