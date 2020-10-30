# Flask & Celery & Redis

> 비동기 처리가 필요한 작업이 있어 적용 과정 중에 기억하기 위해 기록

Flask 외에도 대다수의 웹 프레임워크는 이벤트에 대해 동기적으로 처리하기 때문에 오래 걸리는 작업 예를 들어, 업로드된 파일을 처리한다던가 이메일 전송과 같은 무겁거나 프로세스가 복잡한 연산 작업의 경우 사용자가 요청한 이후에 웹 서버에서 모든 처리가 종료될 때까지 대기해야하는 문제점이 있다. 이런 문제를 해결하기 위해 Celery가 등장하였고 쉽게 설명하면, **<u>비동기 작업 큐 라이브러리</u>**라고 보면 된다. (Flask 외에도 다양한 프레임워크와 사용이 가능하다)

Celery는 대략 3가지의 구성 요소를 지니고 있다.

1. **Celery Client :** 백그라운드 작업을 요청하는데 사용되는데, 여기서는 Flask가 Celery Client로 작업을 요청한다.
2. **Celery Workers** (=Process 혹은 Thread와 유사) : 백그라운드 작업을 진행하는 프로세스라 생각하면 된다.
3. **Message Broker :** 클라이언트가 메시지 큐를 통해 작업자(Workers)와 통신하고 Celery는 이러한 큐를 구현하는 여러가지 방법을 지원한다. 일반적으로 <u>RabbitMQ</u> 및 <u>Redis</u>를 사용한다.

<br>

### Celery Started

#### 설치

```bash
$ pip install celery
or
$ pip install --upgrade celery[redis]
```

<br>

Celery에서는 작업을 Task라고 부르고 있어, `tasks.py`로 파일 생성하여 Celery 작업을 생성하면 다음과 같다.

> 📌 여기서 우리는 `@celery.task` 를 이용해 `task`를 등록하였는데, 이 방법 외에도 Class-based Task도 가능하다. 그건 마지막에 추가할 예정이다.

```python
# tasks.py
from celery import Celery

BROKER_URL = "redis://redis:6379/0"
CELERY_RESULT_BACKEND = "redis://redis:6379/0"

celery = Celery("tasks", brokers=BROKER_URL, backend=CELERY_RESULT_BACKEND)

@celery.task
def custom_task(a:int, b:int):
	result = 0
  for i in range(a, b):
    result += i
   return i
```

1. BROKER_URL : 앞서 설명했던 Message Broker의 URL을 작성한다. 우리는 Redis를 사용하기 때문에 Redis 주소를 입력했다. (Redis의 공식 주소 양식은 `redis://:password@hostname:port/db_number` 이다)
2. CELERY_RESULT_BACKEND : 결과가 저장되는 서버로 보면 되는데, 우리는 Redis 서버에 그대로 결과를 저장하고 리턴 받으려고 한다.

<br>

작성된 Celery를 실행하는 명령어는 다음과 같다.

```bash
$ celery -A tasks worker --loglevel=info
```

* `-A tasks` 에 대한 설명을 얻지는 못하였는데, 여기서 `tasks` 는 파일명으로 보인다.
* `--loglevel=info` : `logging`에 대해 공부하면 알겠지만, logging level 중 info level의 로깅만 설정했다.

```bash
$ celery -A tasks worker --loglevel=info --autoscale=10,3
```

* `--autoscale=10,3` : 최소 3에서 최대 10개의 Worker를 추가하여 동시에 작업이 처리되게 할 수 있다.

<br>

이렇게 작성된 `tasks.py` 는 아래와 같이 `import` 되어 사용 가능하다.

```python
from tasks import custom_task

custom_task.delay(5, 10)
custom_task.wait()
custom_task.get()
```

* `delay` : 작업을 등록하여 Worker가 작업을 진행할 수 있도록 준비 시킨다.
* `wait` : 작업 등록이 완료되고 실행되기 전까지 대기 상태로 유지되며, 작업이 진행되면 종료되고 리턴된다.
* `get` : 작업이 완료되면 결과 값을 리턴해준다.



<br>

## Appendix

### Class-based Task

```python
# custom_task.py
import celery

class CustomTask(celery.Task):
  	# 항상 __init__의 return 값은 None 이여야한다.
	  def __init__(self):
      	self.progress = 0
      
    def run(self, *args, **kwargs):
      	for i in range(100):
        		self.progess = i
  			return self.progress
```

```python
# tasks.py
from celery import Celery

BROKER_URL = "redis://redis:6379/0"
CELERY_RESULT_BACKEND = "redis://redis:6379/0"

celery = Celery("tasks", brokers=BROKER_URL, backend=CELERY_RESULT_BACKEND)

custom_task = celery.register_task(CustomTask())
```

```python
from tasks import custom_task

custom_task.delay()
```