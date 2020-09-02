# Django + React To-Do APP

> ℹ️ [React + Django To-Do App | Django Rest Framework - Dennis Ivy](https://www.youtube.com/watch?v=W9BjUoot2Eo&t=1924s) 보며 공부한 내용입니다. 영상과 달리 typescript를 이용해 React App을 만들었습니다.

### 기술 스택

- Django REST framework
- React.js
- Typescript

<br>

## Django API 만들기

> 백엔드 부분이 굉장히 단순하게 끝났는데, 사실 실무에서는 이것보다 더 복잡해진다. (고건 나중에)

<br>

### Setup

```bash
$ pipenv shell
$ pipenv install django djangorestframework django-cors-headers
```

Python 가상환경은 `pipenv`를 사용했다. 사용해본 경험은 적지만, 편한 부분이 많은 것 같다.

```bash
$ django-admin startproject todo .
$ python manage.py startapp api
```

먼저 To-Do 로 프로젝트 폴더를 생성하였고, 그 안에 api 라는 어플리케이션을 추가했다. 앞으로 백엔드는 api 폴더에서 작업한다고 보면 될 것 같다.

<br>

### Models

```python
# api/models.py
from django.db import models

class Task(models.Model):
    title = models.CharField(max_length=200)
    completed = models.BooleanField(default=False, blank=True, null=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

`Task`라는 모델을 만들었다. Todo App 목적이 결국에는 어떤 일을 해야하고 어떤 일이 남았는지를 기록하는 것이 중심이기 때문에 다음과 같은 필드만 작성한 것 같다.

- `title` : 할 일 제목
- `completed` : 완료 여부
- `created_at` : 작성 시간 (만들어 놓고서는 써먹지를 못했다. 어디에 써보지..)

<br>

### Serializers

```python
# api/serializers.py
from rest_framework import serializers
from .models import Task


class TaskSerializer(serializers.ModelSerializer):
    class Meta:
        model = Task
        fields = "__all__"
```

별 것 없다. 사실 여기서 더 복잡해지면 복잡해질 수 있지만, 그렇게까지 고도화가 필요한 작업은 아니기 때문에 여기까지만 작성한다.

<br>

### views

```python
# api/views.py
from rest_framework import viewsets, permissions

from .serializers import TaskSerializer
from .models import Task

class TaskViewset(viewsets.ModelViewSet):
   queryset = Task.objects.all()
   permissions_classes = [permissions.AllowAny]
   serializer_class = TaskSerializer
```

`viewsets`를 이용하면 정말 간단하게 CRUD를 만들 수 있고 다음과 같은 방법으로 생성/삭제/변경이 가능하다.

- `GET: /api/tasks/` : 목록 가져오기
- `POST: /api/tasks/` : 생성하기
- `PUT: /api/tasks/{id}` : 상태 변경 및 업데이트
- `DELETE: /api/tasks/{id}` : 삭제

<br>

### urls

```python
# api/urls.py
from rest_framework import routers
from .views import TaskViewset

router = routers.DefaultRouter()
router.register("api/tasks", TaskViewset, "tasks")

urlpatterns = router.urls
```

```python
from django.urls import path, include

urlpatterns = [
    path("", include("api.urls")),
]
```

<br>

> 이 다음 정리는 Django 주제와 벗어난다고 보여 Typescript/Todo-App.md 정리하기로 함