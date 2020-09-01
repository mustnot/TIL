# Django + React To-Do APP

> ℹ️ [React + Django To-Do App | Django Rest Framework - Dennis Ivy](https://www.youtube.com/watch?v=W9BjUoot2Eo&t=1924s) 보며 공부한 내용입니다. 영상과 달리 typescript를 이용해 React App을 만들었습니다.

### 기술 스택

- Django REST framework
- React.js
- Typescript

<br>

## 1. Django API 만들기

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

<br>

## 2. React Front 만들기

> ℹ️ 본 영상과 달리 typescript로 작성했습니다.

<br>

### Setup

```bash
$ npx create-react-app frontend --typescript
```

`typescript` 옵션을 두어 frontend라는 이름을 가진 react app을 생성했다. 사실 앞에서 배운대로 하나하나 세팅하려 했는데, `create-react-app`(이하 CRA)로 써보고 무슨 차이점이 있는지, 어떻게 세팅하면 좋을지 비교하기 위해 이번에는 CRA를 이용해 세팅했다.

<br>

### Todo Input Form

사실 Todo App 자체는 단일 페이지로 구성되어 있어 하나의 App 파일만 작성해도 문제가 없다. 입력 폼 만드는 것부터 시작한다. 이번에 작성할 코드는 아래와 같이 `constructor()`와 `render()` 부분이다.

```typescript
import React from "react";
import "./App.css";

class App extends React.Component {
  constructor(props) {
    super(props);
    // ...
  }

  render() {
    // ...
  }
}

export default App;
```

먼저 `render()`를 살펴보면, 가장 큰 `container` 안에 `task-container`가 있고 그 안에 Todo App을 구성하는 `form-wrapper`와 `list-wrapper`가 존재한다. 우리가 먼저 작성할 부분은 `form-wrapper`로 어떠한 할 일을 추가할 것인지에 대해 입력을 받는 곳이 된다.

```typescript
render() {
    let tasks = this.state.todoList;

    return (
      <div className="container">
      	<div id="task-container">
      		<div id="form-wrapper">
      			<form id="form">
      				<div className="flex-wrapper">
      					<div style={{flex: 6}}>
      						<input className="form-control" id="title" type="text" name="title" placeholder="Add task.." />
      					</div>
      					<div style={{flex: 1}}>
                  <input id="submit" className="btn btn-primary" type="submit" name="Add..." />
      					</div>
      				</div>
      			</form>
      		</div>
      		<div id="list-wrapper">
      		</div>
      	</div>
      </div>
    )
  }
```

그 다음 `constructor` 부분인데, 내 생각이지만, 이 부분이 Typescript에서 가장 중요한 부분이지 않을까 생각한다. 그 이유 중 하나는 Typescript 특성상 모든 변수(?)라고 하긴 그렇지만 사용자가 식별 가능한 변수에 대해서는 모두 타입을 지정해줘야하는데, 아래 코드와 같이 Javascript에서 사용하듯이 `Object` 안에 아래와 같이 프로퍼티를 추가하면, 위 `render()` 함수에서 `this.state.todoList` 부분에 `read-only` 에러가 발생한다. 그래서 아래와 같이 `constructor()` 함수를 작성하고 `state` 부분에 대해 `interface`를 설정해줘야 한다.

```typescript
constructor(props) {
    super(props);
    this.state = {
      todoList: [],
      activeItem: {
        id: null,
        title: "",
        completed: false,
      },
      editing: false,
    }
  }
```

`interface`는 다음과 같다. 먼저 `taskState` 를 설정했는데, `task`에는 우리가 Django 모델에서 만들었던 컬럼들을 모두 보유하고 있다.

```typescript
interface taskState {
  id: number | null;
  title: string;
  completed: boolean;
}

interface appState {
  todoList: taskState[];
  activeItem: taskState;
  editing: boolean;
}
```

**tastState :**

- `id` : 자연수 혹은 `null` 허용인데, 여기에 `number`만 쓴다면 `null` 값은 허용되지 않기 때문에 함께 사용하기 위해서는 `number | null` 과 같이 지정해야한다.
- `title` : `string`
- `completed` : `boolean`

**appState :**

- `todoList` : 사용자가 등록한 `task`를 배열로 갖고 있는 변수로 `taskState[]`와 같이 설정하면 된다.
  (이 부분 처음에 상당히 애먹었다.)
- `activeItem` := `task`
- `editing` : 수정 여부 `boolean`으로 프론트에서만 사용되는 변수이다.

이렇게 작업이 완료되면, Todo App의 첫 부분인 입력 부분이 완성된다.

<br>
