# React-Flask-uWSGI-Nginx with Docker

> 한 대의 서버에 React, Flask, uWSGI, Nginx 모두 구성해야할 필요가 있어 나중에 작업을 위해 정리한 내용이다.

<br>

우선 전체 폴더 구성은 다음과 같다.

```bash

```



## Flask

```python
from flask import Flask

app = Flask(__name__)

@app.route("/api/", methods=["GET"])
def index():
    return "hello world"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)

```

