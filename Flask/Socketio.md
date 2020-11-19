## WebSocket vs REST

> 📌 WebSocket에 대해 공부하기 전 WebSocket과 REST의 차이점을 알고 가야할 것 같아 간단하게 정리

### WebSocket

* TCP 연결을 통한 통신 프로토콜로 하나의 TCP 접속에 전이중 통신 채널을 제공하는 통신 프로토콜을 의미
* 전이중 통신 채널을 이용해 기존 HTTP의 장애물을 극복할 수 있다.
* 클라이언트와 서버 간 통신에 관한 표준을 나타내어 개발자로 하여금 모든 플랫폼에서 일관되게 작동하는 기능을 고안할 수 있다.
* 단일 TCP 소켓 연결을 통해 연결 제한 문제 또한 제거한다.
* 서버에 영향 없이 클라이언트 측 코드를 언제든지 변경할 수 없다.

<br>

### REST(Reprensentational State Transfer)

* WWW와 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 아키텍처의 한 형식
* 자원의 표현을 이름으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것을 의미한다.
* 웹 상의 자료를 HTTP 위에서 SOAP나 쿠키를 통한 세션 트래킹 같은 별도의 전송 계층 없이 전송하기 위한 아주 간단한 인터페이스를 말한다.
* 사용자의 상호 작용 빈도가 낮은 HTTP에서 적합하다. 유휴 시간 동안 닫힌 포트 소켓을 통해 리소스를 절약할 수 있기 때문이다.
* 서버에 영향 받지 않고 클라이언트 측 코드를 언제든지 변경 가능하다.

<br>

# Socket.IO

> **⚠️ Socket.IO와 WebSocket은 다르다.**
>
> Socket.IO를 공부하기 전 WebSocket에 대해 정리한 이유는 비슷하게 실시간 양방향 통신을 지원하기 때문이다.

Socket.IO은 실시간 웹 어플리케이션을 위한 JavaScript 라이브러리 중 하나로 이를 통해 클라이언트와 서버 간에 실시간 양방향 통신이 가능하다. [공식 홈페이지](https://socket.io/)을 참고하면 Socket.IO는 실시간, 양방향 및 이벤트 기반 통신을 지원하며 모든 플랫폼, 브라우저 또는 장치에서 작동하며 안정성과 속도에 동일하게 초점을 맞춘다고 되어 있다. 그리고 다음과 같은 곳에서 사용할 수 있다.

1. Real-time analytics : 실시간 카운터, 차트 또는 로그로 표시되는 클라이언트에 데이터를 푸시
2. Instant messaging and chat : 메시징 혹은 채팅 등 실시간 통신에 사용된다.
3. Binary streaming : 1.0 버전부터는 image, audio, video 등을 전송할 수 있다.
4. Document Collaboration : 문서 협업으로 동시에 여러 명이 동일한 문서를 실시간으로 수정 할 수 있다.

<br>

## Flask Socket.IO

**Socket.IO**가 Javascript 라이브러리 중 하나였다면, **Flask-SocketIO**는 Python의 Flask Framework에서 사용 가능한 라이브러리이다. Python 2.7 및 Python 3.3+ 이상의 버전이라면 모두 호환되어 간단하게 설치가 가능하다.

<br>

### Installation

```bash
$ pip install flask-socketio
$ pip install eventlet
```

<br>

### Requirements

설치는 비교적 간단하나 의존하는 다음 3가지 비동기 라이브러리 중 하나를 선택하여 설치해야한다.

* [eventlet](http://eventlet.net/)는 최고의 성능 옵션 중 하나로 long-polling과 WebSocket Transports를 지원한다.

- [gevent](http://www.gevent.org/)는 여러 다른 구성을 지원하는데, eventlet과 동일하게 long-polling transport를 지원하면서도 eventlet과 달리 네이티브 WebSocket을 지원하지 않는다. WebSocket을 지원하기 위해서는 2가지 옵션이 있다. 
  - [gevent-websocket](https://pypi.python.org/pypi/gevent-websocket/)
  - [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/) Web Server
- Werkzeug를 베이스로 작동하는 Flask는 동일하게 uWSGI 서버를 지원하며 오직 long-polling transport만 지원한다.

> ℹ️ **long-polling transport**
>
> 쉽게 설명하면, 일반적인 polling 기법과 달리 가능한 한 오래 동안 서버와 클라이언트 간 연결을 열린 상태로 유지하도록 선택하여 데이터가 사용 가능해지거나 시간 초과 임계값에 도달한 후에만 응답을 제공하는 기술

`eventlet`, `gevent`, `gevent-websocket` 3가지 옵션 중 선택하여 설치하면 된다. 선택 사항이기에 long-polling transport만 지원하는 서버를 개발한다면 추가적으로 설치하지 않아도 된다. 나는 여기서 `eventlet` 을 선택했다. (가장 좋다니깐 뭐.. 나중에 자세히 살펴보자)

<br>

### Initialization

> ⚠️ 이대로 실행 시 간혹 실행이 안되는 문제가 있다. 에러 메세지가 비동기 서비스 문제로 보이는데, 
> 정확한 원인은 파악하지 못하였지만 해결 방법에 대해서는 아래에서 추가로 작성 예정

```python
from flask import Flask
from flask_cors import CORS
from flask_socketio import SocketIO

app = Flask(__name__)
app.secret_key = "secret-key!"
socketio = SocketIO(app, cors_allowed_origins="*")
CORS(app)

if __name__ == "__main__":
    socketio.run(app, host="0.0.0.0", port=8000)
```

(참고 : `init_app()` 스타일의 초기화 방식 역시 지원한다)

문서와 달리 CORS 설정과 함께 작성했다. Flask만 사용한다면 문제가 없지만, 최근에는 다양한 웹 어플리케이션과 함께 사용되기에 CORS와 함께 작성했다. SocketIO에만 `cors_allowed_origins` 옵션을 설정하면 되는 줄 알았는데, Flask에도 CORS를 동시에 설정해주어야한다.

<br>

### Receiving Messages

Socket.IO에서는 클라이언트와 서버 간 Event로 수신 받는다. (작성중 ....)







## Error Log

### ValueError: Too many packets in payload

Socket.IO에 대표적인 예시인 채팅 서버의 경우 주고 받는 데이터가 문자열로 일반적으로 크기가 크지 않다. 하지만 1.0 버전 이후부터 적용된 이미지, 오디오, 비디오의 경우 주고 받는 데이터의 크기가 문자열보다 크기 때문에 위와 같은 문제가 발생할 수 있다. 물론, 크기를 작게 만들어 전달하는 방법이 있으나 이 경우 데이터 손실이 있을 수 있다.

해결 방법은 Payload의 `max_decode_packets`의 크기를 강제로 늘리는 방법으로 설정은 다음과 같다.

```python
from engineio.payload import Payload

Payload.max_decode_packets = ENGINEIO_MAX_DECODE_PACKETS # default 16
```

> ℹ️ Engine.IO 란?
>
> Socket.IO가 아닌 생소한 Engine.IO가 나와 당황했으나, Socket.IO의 내부적인 동작을 담당한다고 생각하면 된다. 일종의 엔진이라고 생각하면 되는데 Engine.IO는 Socket.IO에 비해 기본적인 기능만을 제공하며 동작을 제어하는 역할을 한다고 보면 된다.

에러명만 보면 주고 받는 Packet의 제한을 높여 받을 수 있게하면 되는 것이 아닌가 생각할 수 있는데, 소켓 사이에 전달 받는 데이터의 크기는 uWSGI 혹은 Nginx 등에서 제어하며 서버의 경우 전달 받은 데이터를 `decode` 하여 처리하는 것으로 보인다. 그러다보니 `decode` 할 때 크기가 큰 경우 에러가 발생하는 것으로 보인다. 기본값은 16으로 동일한 이슈에 대한 질답을 참조한 결과 이미지의 경우 500정도만 되더라도 충분한 것으로 보인다.















