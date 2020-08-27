# Full Stack React & Django

> ℹ️ [Full Stack React & Django - Traversy Media (Youtube)](https://www.youtube.com/watch?v=Uyei2iDA4Hs&t=282s)을 보고 따라하며 공부한 내용

<br>

## Basic REST API

> 각 파트에서 사용한 명령어와 코드만 간단하게 작성하고, 코드 내 몰랐던 내용이나 알면 나중에 사용하기 좋은 내용을 간단하게 나마 작성하였다.

해당 파트에서는 간단한 유저 모델로 보이는 `Lead` 모델을 만들어, `rest-framework`의 `serializer`와 `viewsets`를 이용해 간단하게 **REST API**를 만드는 내용이다.

<br>

### Environment Setup

```bash
$ sudo pip3 install pipenv
$ pipenv shell
$ pipenv install django djangorestframework django-rest-knox
```

```bash
$ django-admin startproject leadmanager
$ cd leadmanager
$ python manage.py startapp leads
```

<br>

### Model 작성

> ⚠️ 항상 모델이 변경되거나 추가되었다면 `makemigrate` 명령어를 잊지 말고 작성하자.

```python
# ./leads/models.py
from django.db import models

class Lead(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(max_length=100, unique=True)
    message = models.CharField(max_length=500, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
```

```bash
$ python manage.py makemigrate leads
$ python manage.py migrate
```

<br>

### Serializer 작성

처음에 공부할 때에는 `Class Meta`를 분리하여 작성한 후에 `Serializer`에 붙여서 사용했는데, 안에 작성할 수 있는걸 보고 참고해서 다음에 써서 한 눈에 볼 수 있게 하면 좋을 것 같다.

```python
# ./leads/serializers.py
from rest_framework import serializers
from .models import Lead

class LeadSerializer(serializers.ModelSerializer):
    class Meta:
        model = Lead
        fields = '__all__'
```

<br>

### Views 작성

```python
# ./leads/views.py
from rest_framework import viewsets, permissions

from .models import Lead
from .serializers import LeadSerializer

# Lead Viewset
class LeadViewset(viewsets.ModelViewSet):
    queryset = Lead.objects.all()
    permissions_classes = [
        permissions.AllowAny
    ]
    serializer_class = LeadSerializer
```

* `viewsets` : `APIView`에서 상속된 `ViewSet` 클래스는 API 정책을 제어하기 위한 `permission_classes`와 `certificate_classes` 같은 표준 속성을 제공한다. `ViewSet` 클래스는 작업의 구현을 제공하지 않기 때문에 사용하기 위해서는 클래스를 재정의하고 명시적으로 정의해야한다.

* `permission_classes` : 사용자 정책을 제어하기 위한 클래스
  * `permissions.AllowAny` : 누구나 접근 가능
  * `IsAccountAdminOrReadOnly` : 관리자 혹은 읽기전용 (?)

자세한 내용은 [DRF-Viewsets](https://www.django-rest-framework.org/api-guide/viewsets/#viewset)를 참조하자.

<br>

```python
# ./leads/urls.py
from rest_framework import routers
from .views import LeadViewset

router = routers.DefaultRouter()
router.register('api/leads', LeadViewset, 'leads')

urlpatterns = router.urls
```

```python
# ./leadmanager/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('', include('leads.urls'))
]
```

* `routers` : 여기서 처음 사용해봤는데, 각 `app`마다 Router를 설정할 수 있는걸로 보인다. Flask의 Blueprint 같은 기능으로 보이는데, `register`를 보면 앞서 작성한 `LeadViewset`을 `api/leads`로 등록하여 등록된 url들을 통해 `urlpattern`에 이용하는 걸로 보인다.

<br>

여기까지만 작성하더라도 아래 URL를 통해 `GET/POST/DELETE`를 모두 사용할 수 있다. 이 부분은 정말로 마음에 드는데, 지금까지 Flask를 이용해 API를 만들 때에는 각 메소드에 맞게끔 코드를 짜왔는데, 그런 부분이 일절 사라지면서 짧은 코드 몇 줄만으로 생성/삭제 등이 가능하다는 건 정말로 Django를 매력적으로 만드는 것 같다.

* `GET : /api/leads/`
* `POST : /api/leads/`
* `DELETE : /api/leads/{id}`

<br>

<br>

## Implementing React

이번 파트에서는 Django에서 생성한 어플리케이션에 React를 붙여 사용하는 방법을 설명한다.

<br>

### Environment Setup

```bash
$ python manage.py startapp frontend
$ mkdir -p ./frontend/src/components
$ mkdir -p ./frontend/{static,templates}
```

```bash
$ cd ..
$ npm init -y
$ npm i -D webpack webpack-cli
$ npm i -D @babel/core babel-loader @babel/preset-env @babel/preset-react babel-plugin-transform-class-properties
$ npm i react react-dom prop-types
```

> ⚠️ 이 부분은 나중에 다시 한번 각각 어떤 역할을 하는지 정리하면서 자세히 공부하자.

<br>

```json
# .babelrc
{
    "presets": ["@babel/preset-env", "@babel/preset-react"],
    "plugins": ["transform-class-properties"]
}
```

```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
}
```

```json
# package.json
# ...
"scripts": {
    "dev": "webpack --mode development --watch ./leadmanager/frontend/src/index.js --output ./leadmanager/frontend/static/main.js",
    "build": "webpack --mode production ./leadmanager/frontend/src/index.js --output ./leadmanager/frontend/static/main.js"
}

# ...
```

<br>

### React

> ℹ️ 영상을 보며 공부한 내용은 이 부분보다 더 많이 작성했지만, 리액트 화면이 나오는 부분까지만 작성함

```javascript
// src/index.js
import App from './components/App';
```

```javascript
import React, { Component } from "react";
import ReactDom from "react-dom";

class App extends Component {
  render() {
    return (
      <h1>Hello React</h1>
    );
  }
}

ReactDom.render(<App />, document.getElementById("app"));
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://bootswatch.com/4/minty/bootstrap.min.css">
    <title>Lead Manager</title>
</head>
<body>
    <div id="app"></div>
    {% load static %}
    <script src="{% static "main.js" %}"></script>
</body>
</html>
```

<br>

### Django에 React Index 적용하기

```python
# ./frontend/views.py
from django.shortcuts import render

def index(request):
    return render(request, 'index.html')
```

```python
# ./frontend/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index)
]
```

```python
# ./leadmanager/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('', include('frontend.urls')),
    path('', include('leads.urls')),
]
```

이 부분에서 처음 당황한게 `path`에 두 앱 모두 `''` 로 추가해놓았는데, 정상 작동이 되었다는 점이였는데 이렇게 되면 충돌이 나서 하나는 안 보여지는게 아닐까라고 생각했어 두 가지를 테스트해 보았다.

```python
urlpatterns = [
  path('', include('leads.urls')),
  path('', include('frontend.urls'))
]
```

이 처럼 `api`를 만들었던 `leads.urls`가 가장 윗부분에 오게 되면 충돌이 일어나 `runserver`를 하더라도 React 페이지가 나타나지 않은데, 그 현상에 대해 잠시나마 생각해보았는데, 이럴 경우에는 위에서 입력된 `urls`에서 먼저 맞는 url를 찾아본 후에 없다면 에러 페이지를 리턴하는 반면에 반대로`frontend.urls`에는 `index.html`이 있기 때문에 정상적으로 리턴한다는 점이다. 하지만 의아했던 점은 그럼 `api/leads`도 접근이 안되어야할텐데, 접근이 된다는 점이다. (이건 나중에 한번 찾아보자)