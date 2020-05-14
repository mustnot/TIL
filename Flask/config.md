## ⚙ 설정 다루기

애플리케이션들은 일종의 설정 및 구성을 필요로 한다. 애플리케이션 실행 환경에서 다양한 종류의 설정 값들을 변경 할 수 있다. 예를 들어, 디버깅모드를 변경하거나 비밀 키(secret key)를 설정하거나 그 밖의 다른 환경에 대한 값을 변경시킬 수 있다.

**Flask**는 일반적인 경우 애플리케이션이 구동될 때에 설정값들을 사용할 수 있어야 하도록 설계되었는데, 설정값들은 **하드 코드**(hard code)로 적용 할 수도 있는데, 이 방식은 작은 규모의 애플리케이션에서는 그리 나쁘지 않은 방법일지 몰라도 큰 규모의 애플리케이션의 경우 더 나은 방법이 존재한다.

설정값을 독립적으로 로드하는 방법으로 이미 로드된 설정들 값들 중에 속해 있는 설정 객체(config object)를 사용할 수 있는데, (Flask 객체의 config 속성을 참고) 이 객체의 속성을 통해 Flask 자신의 특정 설정값을 저장할 수 있고 Flask의 확장 플러그인들도 자신의 설정값을 저장할 수 있다.


## ✏ 기초 연습

`config`는 실제로 `dictionary`의 서브클래스이며, 다음과 같이 수정될 수 있다.

```python
app = Flask(__name__)
app.config.update(
    DEBUG=True,
    SECRET_KEY='abcde..'
)
```


## 👍 설정 베스트 사례

> ⛔ 모든 설정 파일이 그러하듯이 공개된 소스 저장소 Git과 같은 잘못 커밋하게 될 경우, 위험하니 각별히 주의하자 !!

1. `Distribute`로 설정하기
* to be continue..

2. `object`로 설정하기

`object`로 설정하기는 여러 가지 방법이 있지만,  `Class`와 상속을 통해서 설정을 활성화 할 수 있다. 

```python
# config.py
class Config(object):
    DEBUG = False
    TESTING = False
    DATABASE_URL = 'sqlite://:memory:'

class ProductionConfig(Config):
    DATABASE_URL = 'mysql://user@localhost/db'

class DevelopmentConfig(Config):
    DEBUG = True

class TestingConfig(Config):
    TESTING = True
```

예시로 `config.py`에 위와 같이 여러 설정을 작성한 후에 아래와 같이 한 줄의 코드로 활성화 할 수 있다.

```python
import config
from flask import Flask

app = Flask(__name__)
app.config.from_object('config.ProductionConfig')

if __name__ == "__main__":
    app.run()
```