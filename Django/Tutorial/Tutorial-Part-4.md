# Django Tutorial Part 4

> Part 3과 이어진다.

<br>

## Write a minimal form

```html
<!-- polls/templates/polls/detail.html -->
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
{% endfor %}
<input type="submit" value="Vote">
</form>
```

`polls/detail.html` 템플릿은 간략하게 설명하면,

* 질문 안 선택 항목에 대한 라디오 버튼을 표시한다.
    * 각 라디오 버튼의 `value`는 연관된 질문 선택 항목의 `ID`다.
    * 각 라디오 버튼의 `name`은 `choice` 입니다.
    * 누군가가 라디오 버튼 중 하나를 선택하여 폼을 제출하면, POST 데이터인 `choice=#`을 보낸다.
* `<form action="{% url 'polls:vote' question.id % }" method="post">`
    * 라디오 버튼에서 선택 항목에 대해 `choice=#`을 파라미터로하여 `action`에 작성되어 있는 `polls:vote`에 `POST`로 전달한다.
* `forloop.count`는 `for` 태그가 반복을 한 횟수
* `{% csrf_token %}`
    * 사이트 간 요청 위조를 방지하기 위해 사용된다.
    * 되도록 모든 `POST` 양식에 위 템플릿 태그를 사용해야한다.

<br>

### vote() 함수 작성

```python
# polls/views.py
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse

from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

* `request.POST['choice']` 는 라디오 버튼에서 보냈던 `POST` 값 중 `choice`에 입력된 값을 가져오는 객체로 사전(dict)와 같은 객체이다. (`flask request.form.get`과 동일한 기능으로 보인다.)
    * Django는 같은 방법으로 `request.GET`을 제공하지만 `POST` 요청을 통해서만 값이 수정되게 하기 위해서 명시적으로 코드에 `request.POST`를 사용함 (결론, 여기에서는 `POST` 요청 맞기 때문에 `POST`로 받음)
    * 만약 `POST`에 `choice`가 없으면 `KeyError`가 발생하고, 만약 `Choice`가 있더라도 질문에 선택지가 없다면 `Choice.DoesNotExist` 에러가 발생한다.
* 설문지의 수가 증가한 경우 `HttpResponse`가 아닌 `HttpResponseRedirect()`를 사용했는데 그 이유는 `form POST` 요청이 성공 했기 때문인데, 사용자의 요청이 성공되면 다른 페이지를 그대로 리턴하기 보다는 성공한 결과를 토대로 다른 페이지를 리다이렉트 하는 방식으로 가야하기 때문이다(?)
    * `HttpResponseRedirect` 생성자 안에서 `reverse()` 함수를 사용하고 있다. 이 함수는 뷰 함수에서 URL을 하드코딩하지 않도록 도와주는데, 제어를 전달하기 원하는 뷰의 이름을 그리고 `args` 를 전달해서 주면 해당 뷰의 전달하여 `return`한다.

<br>

### Results 뷰 만들기

```python
# polls/views.py
from django.shortcuts import get_object_or_404, render

def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```

```html
<!-- polls/templates/polls/results.html -->
<h1>{{ question.question_text }}</h1>

<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```

<br>

## 제너릭 뷰 사용하기 : 적은 코드가 더 좋습니다.

```python
# polls/views.py
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})

def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```

실제로 `views.py`에서 `detail()` 과 `results()`는 코드가 비교적 적게 쓰였는데, 이렇게 `views.py`는 되도록이면 적은 코드가 좋다. 하지만, 이러한 뷰는 URL에서 전달 된 매개 변수에 따라 데이터베이스에서 데이터를 가져 오는 것과 템플릿을 로드하고 렌더링 된 템블릿을 리턴하는 일반적인 경우인데, 이러한 일반적인 경우 Django는 "**제너릭 뷰**" 를 제공한다.

제너릭 뷰는 일반적인 패턴을 추상화하여 앱을 작성하기 위해 Python 코드를 작성하지 않아도 된다. 제너릭 뷰 변환 순서는 다음과 같다.

1. URLconf를 수정한다.
2. 불필요한 오래된 보기 중 일부를 삭제
3. Django의 제너릭 뷰를 기반으로 새로운 뷰를 도입

<br>

```python
# polls/urls.py
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

`urls.py`는 크게 바뀐 것이 없다. `<int:question_id>` 에서 `<int:pk>`로 바뀐 것 뿐이다.

<br>

```python
# polls/views.py
from django.http import HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic

from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'


class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'


def vote(request, question_id):
    ... # same as above, no changes needed.
```

`ListView`와 `DetailView`의 두 가지 제너릭 뷰를 사용하고 있다.

* `ListView` : 개체 목록 표시
* `DetailView` : 특정 개체 유형에 대한 세부 정보 페이지 표

각 제너릭 뷰는 어떤 모델이 적용될 것인지 알아야하는데, 이것은 `Model` 속성에 영향을 받는다. `DetailView` 제너릭 뷰는 URL에서 캡쳐된 기본 키 값이 `pk`라고 기대한다. 그렇기 때문에 `question_id`에서 `pk`로 변경되었다.

* 나중에 보면 좋을 것 같은 내용
    * CBV (Class-based View) vs FBV (Function-based View)
    * [Django CBV - 제너릭뷰](https://docs.djangoproject.com/ko/3.0/topics/class-based-views/)