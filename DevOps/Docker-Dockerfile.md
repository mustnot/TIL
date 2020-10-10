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

