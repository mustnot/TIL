## Flask app 생성

우선 `/srv` 폴더에 `Flask` 프로젝트를 생성하고, 그 안에 `venv` 로 환경세팅 후 `flask` 를 설치하였다. 

```bash
$ cd /srv/
$ mkdir Flask
$ python3 -m venv .venv
$ source .venv/bin/activate
(.venv) $ pip install flask
```

간단하게 Flask Application을 아래와 같이 작성하였다. 큰 기능들은 없고 간단하게 작성했다.

```python
# app.py
import uuid
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/user/<string:username>', methods=['GET'])
def user(username):
    message = {
        "status": 200,
        "username": username,
        "uid": str(uuid.uuid4())
    }

    return jsonify(message), 200

@app.route('/', methods=['GET'])
def ping():
    message = {
        "status_code": 200,
        "message": "success"
    }
    return jsonify(message), 200
```

```python
# run.py
from app import app

if __name__ == "__main__":
    app.run(host='127.0.0.1', port=8000)
```

<br>

# AWS Settings

`Zappa`  만 사용하면 손쉽게 `Lambda` 에 접근하여 배포할 수 있다고 생각했는데, 구글에 나오는 몇 가지 페이지에서 읽어본 순서대로 아무리 해도 되지 않았다. (역시 삽질이 최고다.) 그래서 Reference에 있는 링크에 접속해서 읽어보니, 생각보다 세팅해야할 것들이 많았고, 세팅 과정에 대해 정리해보았다.

<br>

## IAM 사용자 자격 증명 관리

IAM이란, Identity and Access Management의 약자로 AWS 리소스에 대한 액세스를 제어할 수 있는 일종의 자격 증명 관리 서비스이다. IAM를 사용하여 AWS 리소스를 사용하도록 사용자를 생성하고 권한을 부여할 수 있다. 생각해보면, 배포를 위한 계정과 관리를 위한 계정을 분리해서 사용했던 기억이 있다. (deploy 계정이라던가로...) 쉽게 생각하면, 관리자 계정을 통해 배포를 진행하는 것보다, 배포를 위한 계정을 생성하고 배포만을 위한 권한을 제공하는 것이 좋은 방법이라 생각된다.

![6FE1B293-F2C4-46BC-8B9E-757E39C5B11F](https://user-images.githubusercontent.com/52126612/85918215-a40d5400-b89b-11ea-8878-da9b1a03352f.jpg)

사용자 - 사용자 추가에서 나는 `zappa-lambda-deploy` 라는 사용자를 생성했다. 나중에는 계정명과 목적이 바뀔 수도 있겠지만, 우선 Zappa를 이용한 배포 계정으로 생성하였다.

<br>

## EC2에 awscli 환경 구성하기

사용자 추가 과정 마지막에 생성되었던, Access Key와 Secret Access Key를 입력한다. region name은 나의 경우 ap-northeast-2로 설정했다.

```bash
$ sudo apt install awscli
$ aws configure
AWS Access Key ID [None]: ******
AWS Secret Access Key [None]: ******
Default region name [None]: ap-northeast-2
output format [None]: json
```

<br>

# Zappa

`Zappa` 설치는 생각보다 매우 간단했다. `pip install zappa` 만으로도 설치되는 "아주" 간단한 설치 방법이다. 이후 `zappa init` 을 실행하면, 설정 파일을 생성하는 과정을 거치는데, 나는 위 과정 없이 처음부터 막무가내로 설정을 진행했는데, 그렇게 할 경우 마지막에 `deploy` 가 되지 않는다. 😅

```bash
(.venv) $ pip install zappa
(.venv) $ zappa init
What do you want to call this environment (default 'dev'): dev
What do you want to call your bucket? (default 'zappa-*******'): 
Where is your app's function? (default 'app.app'): app.app
Would you like to deploy this application globally? (default 'n') [y/n/(p)rimary]: n
```

```bash
$ cat zappa_settings.json
{
    "dev": {
        "app_function": "app.app",
        "aws_region": "ap-northeast-2",
        "profile_name": "default",
        "project_name": "flask",
        "runtime": "python3.6",
        "s3_bucket": "zappa-*******"
    }
}
$ zappa deploy dev
...
Deployment complete!: https://******.execute-api.ap-northeast-2.amazonaws.com/dev
```

`zappa init` 으로 설정이 모두 완료되면, `zappa_settings.json` 가 생성되는데 내용을 보면 앞서 설정한 내용을 토대로 파일이 생성된다. 그 후 `zappa deploy dev` 명령어를 통해 ` deploy` 를 실행하게 되고 완료되면 `Deployment complete!` 와 함께 `lambda` 주소를 알려주고 해당 주소를 이용해 요청하면 위에서 작성했던 코드대로 `response` 를 받을 수 있다.

<br>

## 결과

위 과정을 전부 다 거치게 되면 AWS 내 `Lambda` 에는 `flask-dev` 라는 함수가 생성되고   `S3` 에는 `zappa-*******` 로 버킷이 생성되어 있다.

![37C01B26-9C30-4D2D-AF04-3AF00E3DF3C6](https://user-images.githubusercontent.com/52126612/85918219-a7a0db00-b89b-11ea-84c9-4f6b85ec24a4.jpg)
![62932DD4-1341-43EE-9410-784FB97338AB](https://user-images.githubusercontent.com/52126612/85918220-a8397180-b89b-11ea-90c4-802994f405f2.jpg)

<br>
<br>

## Reference

* [How to create a serverless service in 15 minutes - freeCodeCamp](https://www.freecodecamp.org/news/how-to-create-a-serverless-service-in-15-minutes-b63af8c892e5/)
* [Zappa를 이용해 AWS Lambda에 Flask 올리기](http://dveamer.github.io/backend/FlaskZappaAWSLambda.html)

