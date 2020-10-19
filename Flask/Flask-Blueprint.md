## Blueprint?

**블루프린트**(blueprint)는 **청사진**이라고도 부르는데, 사전적 의미로는 아키텍처 또는 공학 설계를 문서화된 기술 도면을 인화로 복사하거나 복사한 도면을 말한다. 그럼 **Flask**에서 **블루프린트**(blueprint)는 어떤 개념일까?

**플라스크**에서 **블루프린트**는 보통 대형 애플리케이션이 동작하는 방식을 구조화하고 단순화하여 플라스크 **확장**에 대해 중앙 집중적으로 관리할 수 있는 기능을 제공한다. 쉽게 보면, 시간이 지날수록 기존 애플리케이션에서 점차 확장할 수 밖에 없는 상황에 이르게 되는데, 이를테면 페이지의 증가, API 개수 증가 등 점진적으로 플라스크 애플리케이션은 확장하게 된다.

문제는 기존에 계획되었던 구조와 달리 확장을 거치게되면 구조가 변화할 수 밖에 없는데, 이런 경우 플라스크의 블루프린트 기능은 각 애플리케이션을 모듈화하여 플라스크 레벨에서 분리하거나 추가할 수 있는 기능을 제공하는 것이다. (물론 플라스크에서 하지 않고 미들웨어를 통해서 분리하거나 추가할 수도 있다.)

## Blueprint Example

```bash
# example 1
flask_app
├── blueprint_app_1
│   ├── __init__.py
│   ├── views.py
│   ├── templates
│   │   └── index.html
│   └── static
│       └── style.css
├── blueprint_app_2
│   ├── __init__.py
│   ├── views.py
│   ├── templates
│   │   └── index.html
│   └── static
│       └── style.css
└── run.py
```

```bash
# example 2
flask_app
├── blueprint_apps
│   ├── __init__.py
│   ├── app_1
│   │   ├── __init__.py
│   │   ├── views.py
│   │   ├── templates
│   │   │   └── index.html
│   │   └── static
│   │       └── style.css
│   ├── app_2
│   │   ├── __init__.py
│   │   ├── views.py
│   │   ├── templates
│   │   │   └── index.html
│   │   └── static
│   │       └── style.css
└── run.py
```

### 순환 임포트 문제 : Circular Import Problem

사실 순환 임포트 문제가 있긴 하지만, 그렇게 큰 문제는 아니다. 그 이유는 구조 상으로 서로 다른 블루프린트 애플리케이션 사이에 임포트가 꼬여서 서로 다른 상태에서의 파일을 임포트 하지만 않으면 큰 문제가 없다. 또한 해결하기 가장 쉬운 방법은 `__init__.py`에 사용되는 뷰들만 임포트하여 불필요하게 임포트 되는 모듈과 파일을 분리하면 된다. (만약 `__init__.py`의 역할을 모른다면, [패키지-점프투파이썬(Wikidocs)](https://wikidocs.net/1418) 보고 간단히 공부하자!)

<br>

## Code Example

간단히 나마 코드로 예시를 들면 다음과 같다.

```python
# flask_app/admin/views.py
from flask import Blueprint, request

admin_app = Blueprint('admin', __name__, url_prefix="/api")

@admin_app.route('/sign', methods=['GET'])
def sign():
    return 'is domain_app route /sign'
```

```python
# flask_app/run.py
from flask import Flask
from admin.views import admin_app

app = Flask(__name__)
app.register_blueprint(admin_app)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=8080)
```

* `url_prefix` : Flask Blueprint에는 `url_prefix`를 지정할 수 있는데, 이를 통해서 URI의 일부를 고정할 수 있다.