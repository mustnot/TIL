## Django Tutorial Part 1

```bash
$ python -m django --version
3.0.8
```

먼저 Django 설치가 되었다고 가정하고, 설치된 Django의 버전을 확인하여 설치가 제대로 되었는지 확인한다. 

<br>

### 프로젝트 만들기

Django는 초기 설정에 주의를 기울여야한다. 그 이유는 Django Project를 시작할 때 구성하는 코드를 자동 생성해야하는데, 이 과정에서 데이터베이스 설정, Django를 위한 옵션들, 애플리케이션을 위한 설정들과 같은 Django 인스턴스를 구성하는 수많은 설정들이 생성되기 때문이다. 다음 명령어를 실행하면 프로젝트를 생성한다.

```bash
$ django-admin startproject mysite
$ tree
.
└── mysite
    ├── manage.py
    └── mysite
        ├── __init__.py
        ├── asgi.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```

<br>

프로젝트를 생성하고 나면 위 tree 결과처럼 프로젝트 폴더를 하나 생성하고 그 안에 `manage.py`와 `mysite`라는 디렉토리를 생성한다. 이 과정이 정상적으로 이루어졌다면, `django-admin` 실행에 문제가 없다는 것을 의미한다.

> 이 때 프로젝트 생성시 Django에서 이미 사용 중인 이름은 피해야한다. 아마 Python에서 내부 모듈 참조시 충돌이 일어나기 때문으로 생각되며 대표적인 예로는 _django, _test 등이 이에 속한다.

<br>

생성된 프로젝트 내 `mysite` 디렉토리를 자세히 살펴보면 다음과 같다.

| directory or file    | description                                                  | Reference                                                    |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `manage.py`          | Django 프로젝트와 다양한 방법으로 상호작용하는 커맨드라인의 유틸리티 | [django-admin and manage.py](https://docs.djangoproject.com/ko/3.0/ref/django-admin/) |
| `mysite/`            | 디렉토리 내부에는 프로젝트를 위한 실제 Python 패키지들이 저장된다. |                                                              |
| `mysite/__init__.py` | Python으로 하여금 이 디렉토리를 패키지처럼 다루라고 알려주는 용도의 단순한 빈 파일 |                                                              |
| `mysite/settings.py` | 프로젝트의 환경 및 구성을 저장                               | [django-settings](https://docs.djangoproject.com/ko/3.0/topics/settings/) |
| `mysite/urls.py`     | 프로젝트의 URL 선언을 저장합니다.                            | [djagon-url-dispatcher](https://docs.djangoproject.com/ko/3.0/topics/http/urls/) |
| `mysite/asgi.py`     | ASGI (잘 모르겠다)                                           |                                                              |
| `mysite/wsgi.py`     | 프로젝트를 서비스하기 위한 WSGI 호환 웹 서버의 설정          | [How to deploy with WSGI](https://docs.djangoproject.com/ko/3.0/howto/deployment/wsgi/) |

<br>

### 서버 실행하기

```bash
$ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

August 03, 2020 - 12:17:21
Django version 3.0.9, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

<br>

<br>

### 설문조사 앱 만들기

Django에서는 App (앱)의 기본 디렉토리 구조를 자동으로 생성할 수 있는 도구를 제공하여 코드에만 집중할 수 있는 환경을 제공합니다. 

```bash
$ python manage.py startapp polls
$ tree
├── db.sqlite3
├── manage.py
├── mysite
│   ├── __init__.py
│   ├── asgi.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── polls
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations
    │   └── __init__.py
    ├── models.py
    ├── tests.py
    └── views.py
```

<br>

`python manage.py startapp <appname>`을 이용하여 `app`을 생성하면 위와 같은 형태의 디렉토리를 통해 구조를 만들어준다. 위에서 언급한 바와 같이 Django는 개발자로 하여금 코드에만 집중할 수 있는 환경을 제공해주는 것 같아서 이런 부분은 정말로 마음에 들었다.

> Flask를 다시 보면 맨땅에 헤딩한 기분이랄까..?

<br>

### 첫 번째 뷰 작성하기

```python
# mysite/polls/views.py
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

```python
# mysite/polls/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

```python
# mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

가장 먼저 `polls/views.py`에 `request` 을 인수로 받는 함수 `index`를 생성하고 `return`은 `HttpResponse`로 텍스트 형태의 리스폰스를 받는 코드를 작성하였다. 그 후 기존에 `startapp`으로 처음 디렉토리가 생성되었을 때에는 없었던 `urls.py` 파일을 생성한 후 `polls` 폴더에 있는 `views`를 `import`하여 `path` 경로를 설정해주었다. 이후 이렇게 매핑된 `urls.py` 를 가장 최상위 단계의 `URLconf` 에서도 참조할 수 있도록 코드를 짜주면 앞으로 `/polls/` 로 들어오는 모든 요청에 대해서 `views.py`에 작성한 `HttpsResponse`를 리턴한다.

>여기서 include() 란, 다른 URL 패턴을 포함할 때마다 항상 include()를 사용해야하며 admin.site.urls 가 유일한 예외가 된다. Flask의 Blueprint와 동일한 기능이라 생각하면 좋을 것 같다
>(하면 할수록 flask의 blueprint와 django의 app이 비슷하다고 생각되는건 맞는지 모르겠다.)



### path(route, view, kwargs, name)

**route** : URL 패턴을 가진 문자열로 Django는 urlpatterns의 첫 번째 패턴부터 시작하여, 일치하는 패턴을 찾을 때까지 요청된 URL을 각 패턴과 리스트의 순서대로 비교합니다.

> 그렇다면 URL이 굉장히 많은 대규모 서비스라면, path의 순서를 변경해주는 것만으로도 속도 개선이 있을 수 있겠군

**view** : Django에서 일치하는 패턴을 찾으면, **HttpRequest** 객체를 첫 번째 인수로 하고 경로로부터 "캡쳐된" 값을 키워드 인수로 하여 특정한 view 함수를 호출합니다.

**kwargs** : 임이의 키워드 인수들은 목표한 view에 사전형으로 전달됩니다.

**name** : URL에 이름을 지으면, 템플릿에 포함한 Django 어디에서나 명확하게 참조할 수 있습니다. 그렇기에 이 기능을 사용할 경우, 단 하나의 파일만 수정해도 project 내의 모든 URL 패턴을 바꿀 수 있습니다.