# Docker Command

> ℹ️ 이전에 공부했던 명령어들이 있어 해당 부분과 추가로 공부한 내용을 추가했다.

<br>

## 명령어 정리

1. `docker ps`

   * 기본 정보

     | Column       | Description                                                  |
     | ------------ | ------------------------------------------------------------ |
     | CONTAINER ID | 컨테이너의 고유한 아이디 해쉬값으로 실제로는 더 길다.        |
     | IMAGE        | 컨테이너 생성시 사용한 도커 이미지                           |
     | COMMAND      | 컨테이너 시작시 실행될 명령어로 대부분 이미지에 내장되어 있어 별도 설정이 필요 없다. |
     | CREATED      | 컨테이너가 생성된 시간                                       |
     | STATUS       | 컨테이너의 상태<br />실행 중 : Up, 종료 : Exited, 일시정지 : Pause |
     | PORTS        | 컨테이너가 개방한 포트와 호스트에 연결한 포트                |
     | NAMES        | 컨테이너 고유한 이름으로 컨테이너 생성시 --name 옵션으로 설정<br />설정하지 않은 경우 임의의 형용사와 명사를 조합해 설정한다.<br />`docker rename original-name changed-name` |

   * 지정된 정보만 출력

     * `docker ps --format 'table{{.Names}}\table{{.Image}}'`

   * 종료된 컨테이너도 모두 보기

     * `docker ps -a`

2. `docker pull [IMAGE NAME]:[TAG]` :  이미지 가져오기

3. `docker images` : 이미지 리스트 보기

4. `docker rmi [IMAGE NAME]` : 이미지 제거

   1. `-f, —force` : 이미지를 강제로 삭제
   2. `—no-prune` : 태그가 없는 부모 이미지를 삭제하지 않습니다.
   3. `docker rmi $(docker images -q)` : 전체 이미지 제거

5. `docker run` : 컨테이너 실행

   - `docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]`

     | Option | Description                                                  |
     | ------ | ------------------------------------------------------------ |
     | -d     | detached mode로 흔히 말하는 백그라운드 모드                  |
     | -p     | 호스트와 컨테이너의 포트 연결 (포워딩)                       |
     | -v     | 호스트와 컨테이너의 디렉토리 연결 (마운트)                   |
     | -e     | 컨테이너 내에서 사용할 환경 변수 설정                        |
     | -name  | 컨테이너 이름 설정                                           |
     | -rm    | 프로세스 종료시 컨테이너 자동 제거                           |
     | -it    | -i와 -t를 동시에 입력한 것으로 터미널 입력을 위한 옵션<br />`-i : interactive, -t : terminal` |
     | -link  | -link 컨테이너 연결 [컨테이너명:별칭]                        |

6. `docker exec` : 컨테이너 명령어 전달

   - `docker exec -it [CONTAINER ID or NAME] sh` : interactive 하게 터미널 사용하기 

7. 컨테이너 종료

   * `exit` or `ctrl + D` : 컨테이너 종료하고 빠져나오기

   * `ctrl + P, Q` : 컨테이너 종료시키지 않고 나오기 (다시 들어가려면 `docker attach [CONTAINER NAME])`

8. `docker ps` : 실행중인 컨테이너 목록 확인

9. `docker cp` : 도커 파일 복사

   - `docker cp [PATH] [CONTAINER ID or NAME]:[CONTAINER PATH]` : 로컬 파일을 컨테이너 안으로 복사
   - `docker cp [CONTAINER ID or NAME]:[CONTAINER PATH] [PATH]` : 컨테이너 안에 있는 파일을 로컬로 복사
   
10. `docker system prune` : 모든 컨테이너, 이미지, 네트워크 삭제

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