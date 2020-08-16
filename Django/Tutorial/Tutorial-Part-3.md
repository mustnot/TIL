## Django Tutorial Part 3

>  Part3 에서는 투표(polls) 어플리케이션에 공개 인터페이인 "뷰(View)"를 추가하는 단계

블로그 어플리케이션을 예로 들어 뷰를 설명하면 다음과 같은 구조를 가진다.

> 나중에 한 번 블로그를 django로 만들어보는 것도 재밌을 것 같다. admin 계정 하나 만들고 markdown으로 글 추가 삭제 가능하도록 !

* Blog 홈페이지 - 가장 최근의 항목들을 표시
* 항목 세부(detail) 페이지 - 하나의 항목에 연결하는 영구적인 링크(permalink)를 제공
* 년도별/월별/날짜별 축적 페이지 - 주어진 연도/월/날짜의 모든 월별/날짜별 항목들을 표시
* 댓글 기능 - 특정 항목에 댓글을 다룰 수 있는 기능

<br>

우리가 만들고자하는 투표(polls) 어플리케이션의 뷰는 어떻게 구성될까

* 질문 "색인" 페이지 - 최근의 질문들을 표시
* 질문 "세부" 페이지 - 질문 내용과 투표할 수 있는 서식을 표시
* 질문 "결과" 페이지 - 특정 질문에 대한 결과를 표시
* 투표 기능 - 특정 질문에 대해 선택할 수 있는 투표 기능을 제공

<br>

## 뷰 추가하기

```python
# polls/views.py
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")

def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)

```

```python
# polls/urls.py
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

<br>

이 과정을 따라하면서 나는 `urlpatterns`에 `example`을 작성해놓은 것이 정말로 따라하면 좋을 코드라고 생각했다. 왜냐하면 나도 간혹 `flask`를 사용하다가 `app.route` 에 위와 같은 형태로 작성해놓더라도 코드를 다시 보고 기억하는데, 저렇게 작성되어있다면 `ex`만 보고 이해하면 되니 좋은 것 같다.

`views.py`에는 앞서 뷰에 무엇이 있을 것이라고 가정한 뷰 리스트를 그대로 작성했고, 리턴 값은 `HttpResponse`에 `text`를 리턴한다. 어찌보면 이 방법은 좋은 방법이라 생각한다. 먼저 어떠한 뷰가 나올지 계획한 후에 계획에 맞춰 뷰를 만들어놓으면 추후에 코드를 작성하더라도 위나 아래에 코드를 보고 맞춰서 개발하기에 편의성이 높다고 생각한다.

<br>

### 뷰가 실제로 무언가 하도록 만들기

위에서 작성한 `views.py`는 어떠한 행동으로 인한 결과를 보여주기보다는 특정 페이지가 잘 보여지고 있는지에 대한 간략한 테스트로 작성한 코드라고 보면 된다. 뷰는 두 가지 중 하나를 하도록 되어 있다. 요청된 페이지의 내용이 담긴 `HttpResponse` 객체를 반환하거나, 혹은 `Http404` 같은 예외를 발생하게 해야한다. (404 : Not Found)

```python
# polls/views.py
from django.http import HttpResponse
from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

#...
```

<br>

위 코드는 예시이다. 만약에 위와 같이 뷰 함수를 짠다면 어떻게 될까? 답은 간단한데 그저 텍스트로 질문들만 쉼표와 함께 나열될 것이고 어떠한 아름다운 화면도 어떠한 디자인도 볼 수 가 없을 것이다. 그래서 `templates` 라는 디렉토리를 만들어 Django는 사용자의 요청에 따라 템플릿을 찾아 화면에 보여주게 될 것이다.

> 📌 템플릿 네임스페이싱
>
> 예를 들어 index.html 이라는 템플릿이 있다고 보면, 이 index.html은 굉장히 어디서나 많이 사용되는 템플릿 중 하나로 특정 앱에 대부분 상관없이 메인 화면에서 사용된다고 볼 수 있는데, 만약에 index.html을 하나의 디렉토리에 저장하여 불러온다면 어떻게 될까? 그리고 여러 어플리케이션들이 존재하고 각 어플리케이션마다 index.html을 호출한다면 어떻게해야할까? 라는 질문에 답을 생각해보는 것이 좋다고 생각한다.

<br>

```html
# polls/templates/polls/index.html
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li>
        	<a href="/polls/{{ question.id }}/">{{ question.question_text }}</a>		</li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

```python
# polls/views.py
from django.http import HttpResponse
from django.template import loader
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {'latest_question_list': latest_question_list}
    return HttpResponse(template.render(context, request))

# ...
```

먼저 `Question Model`에서 `pub_date`를 기준으로 내림차순된 데이터를 5개 가져와 `latest_question_list`에 넣는다. 이후 템플릿을 `loader`을 이용해 가져오고,` context`라는 변수에 템플렛에서 사용한 변수명과 일치시켜 저장한다. 이렇게 저장된 `context`를 템플릿과 함께 `render`한 결과를 `return` 한다.

<br>

### 지름길 : render()

```python
from django.shortcuts import render
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

<br>

<br>

## 404 에러 일으키기

```python
from django.http import HttpResponse, Http404
from django.template import loader

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except:
        raise Http404("Question does not exist")
    
    template = loader.get_template('polls/detail.html')
    context = {'question': question}
    return HttpResponse(template.render(context, request))
```

```html
# polls/templates/polls/detail.html
{{ question }}
```

<br>

### 지름길 : get_object_or_404()

```python
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

`get_object_or_404(model, kwargs)` 함수는 모델을 첫 번째 인수로 받아 몇 개의 키워드 인수를 모델 관리자의 `Object.get` 함수에 넘기는데, 만약 존재하지 않을 경우, `Http404` 예외가 발생한다.

> `get_object_or_404` 외에도 `get_list_or_404` 도 있어, 만약 object가 아닌 list 형태의 결과일 경우에 사용할 수 있으며, 리스트가 비어 있을 경우에 `Http404` 예외를 발생시킨다.

<br>

<br>

## 템플릿 시스템 이용하기

직전에 `detail.html`에는 간단하게 `{{ question }}` 만 화면에 보여주는 형식으로 질문만 보여주었는데, 아래는 반복문을 사용하여 `Question` 이외에도 선택지의 목록을 보여줄 수 있게 했다.

```html
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

템플릿 시스템은 변수의 속성에 접근하기 위해 점-탐색(dot-lookup) 문법을 사용한다. 예제의 `{{ question.question_text }}` 구문을 보면, 먼저 `question` 객체에 대해 사전형으로 탐색하고 만약 실패하게 되면 속성 값을 탐색한다. 여기서도 실패하면 리스트의 인덱스 탐색을 시도한다. 아래 순서로 탐색한다.

1. `question.question_text`
2. `question.choice_set.all`
    * `choice.choice_text`

<br>

## 템플릿에서 하드 코딩된 URL 제거하기

`polls/iindex.html` 템플릿에 링크를 적으면, 다음과 같이 부분적으로 하드코딩된다.

```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

위 템플릿은 index.html에서 질문을 선택하게 되면 해당 질문의 디테일로 링크되어 이동하는데, 아래처럼 작성시 하드코딩되어 URL을 바꾸는게 어려운 일이 될 수 있다. 그래서 뷰에 종속되게 변경할 수 있다.

```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

여기서 `url 'detail'` 은 `urls.py` 에 작성한 `name`에 영향을 받는다.

```python
# the 'name' value as called by the {% url %} template tag
path('<int:question_id>/', views.detail, name='detail')
```

<br>

## URL의 Namespace 정하기

튜토리얼 프로젝트의 `polls`라는 앱 하나만 가지고 진행했는데, 실제 프로젝트는 앱이 몇개라도 올 수 있는데, 그럼 Django는 어떻게 이 앱들의  URL을 구별해낼까? `polls` 앱은 `detail`이라는 뷰를 가지고 있고, 동일한 뷰를 가지는 다른 앱이 존재할 수 있는데도 말이다. Django가 `{% url %}` 템플릿 태그를 사용할 때, 어떤 앱의 뷰에서 URL을 생성할지 알 수 있을까?

> 이 부분과 연관된 내용을 앞서서 생각해본 적이 있다. 위 예시에서는 detail을 예로 들었지만, 실제로 index가 될수도 있고 여러가지 케이스가 있다. 그렇다고 <app_name>_index로 뷰를 만들진 않을테니!

해결 방법은 URLconf에 이름 공간 (namespace) 을 추가하는 것으로 `polls/urls.py` 파일에 `app_name`을 추가하여 애플리케이션의 이름 공간을 설정할 수 있다.

```python
from django.urls import path

from . import views

# app_name 별도의 라이브러리가 필요하지 않다.
app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

```html
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```

