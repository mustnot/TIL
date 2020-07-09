## flask + pytest / pytest + flask

그럼 웹 프레임워크에서는 테스트를 어떻게 진행하는지가 사실 가장 중요한 포인트다. 함수 생성해서 테스트하는 건 매우 쉬웠는데 말이다. 그래서 간단하게  `flask`를 이용해 `API`를 몇 가지 만들고 이를 테스트 해보자!

```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/square/<int:x>', methods=['GET'])
def square(x):
    return jsonify(
        {
            'status': 200,
            'result': 'sucesss',
            'data': x ** 2
        }
    )
if __name__ == "__main__":
    app.run(debug=True)
```

`app`은 간단하게 `/square/<int:x>` 로 만들었고, 정수 `x`를 받아 제곱을 하여 리턴하는 `api`를 생성하였다. 결과 예시는 `/square/5` 요청 시 25를 결과로 받을 것이고, `square/10`인 경우에는 100을 리턴 받을 것이다. 그럼 테스트를 해보자 테스트 코드는 다음과 같이 작성한다.

```python
# test_api.py
import pytest
from app import app

@pytest.fixture
def client():
    client = app.test_client()
    return client

def test_square(client):
    response = client.get('/square/5')
    assert response.json['data'] == 25
    
    response = client.get('/square/10')
    assert response.json['data'] == 100
```



먼저 `pytest.fixture`를 설정해준다. `fixture`는 `unittest`에서 설명했지만 다시 한번 살펴보고 가면, 공식 문서 설명에 의하면 1개 또는 그 이상의 테스트를 수행할 때 필요한 **준비**와 그와 관련된 정리 동작에 해당한다. 어떻게보면 준비 단계를 의미하는데, 여기서는 `flask app`를 테스트하기 때문에 `flask app`의 `test_client()`가 그 준비가 되며 `fixture`단계에 실행된다.

그럼 결과를 보면 이전과 동일하게 pytest 결과화면이 뜬다.

```bash
$ pytest
============================== test session starts ==============================
platform darwin -- Python 3.7.3, pytest-5.4.3, py-1.9.0, pluggy-0.13.1
rootdir: ./Python/study
collected 1 item                                                                

test_api.py .                                                             [100%]

=============================== 1 passed in 0.13s ===============================
```



