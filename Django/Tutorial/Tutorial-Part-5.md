# Django Tutorial Part 5

> Part 4와 이어지는 내용으로 자동화된 테스트를 주제로 한 파트이다.

<br>

## 자동화된 테스트 소개

테스트는 다양한 수준에서 작동하는데, 여기서 수준이란 아주 작은 세부 사항일수도 있고 가장 큰 범위의 사항일수도 있다는 뜻으로 이해하면 된다. 이렇듯 테스트는 작은 세부 사항에 적용한다면 "특정 모델 메서드가 예상된 값을 반환하는지 여부라던지" 같은 수준에 사용될 수 있고 또 다른 테스트는 소프트웨어의 전반적인 작동을 테스트할 수도 있다. 예를 들어 웹사이트 내에서 사용자의 입력 결과가 과정에 맞춰 원하는 결과를 생성하는지 등을 말한다.

Django Tutorial 중 Part2 에서 사용한 것과 마찬가지로 `django-admin shell` 을 이용한 것도 데이터의 입력을 테스트한 것과 마찬가지이다. 테스트 자동화는 시스템에서 수행되는데, 한 번 테스트 세트를 작성한 이후에는 앱을 변경할 때마다 수동으로 테스트를 수행하지 않아도 원래 의도대로 즉 기존에 작상한 테스트 세트에 의해 코드가 작동되는지 확인할 수 있다.

<br>

## 테스트를 만들어야하는 이유

지금까지 튜토리얼을 따라서 작성해본 `polls` 어플리케이션은 가시적으로 보더라도 꽤 잘 돌아가고 있고, 충분히 잘 돌아가고 있기 때문에 굳이 하나하나 따져가면서 테스트를 만드는 것이 어플리케이션을 기존보다 더 잘 돌아가거나 더 좋게 하지는 않기 때문에 어떻게보면 테스트가 굳이? 필요 없다고 여길 수 있다. 하지만, 현재 상태의 `polls` 어플리케이션은 아직 미완성의 단계이고 더 많은 작업이 필요한 상황이기 때문에 테스트를 작성한다면, 추후의 작업이 있더라도 기존의 결과물과 동일하게 잘 돌아갈 것이고 만약 문제가 생긴다면 바로 알아차릴 수 있을 것이다. (이렇듯 완전히 완성된 제품의 경우에는 테스트가 필요 없을지 몰라도, 개발 중이거나 앞으로도 개발해야하는 제품일 경우에는 테스트는 불가피하고 이를 편리하게 하기 위해서는 자동화된 테스트가 필요하다.)

<br>

### 테스트의 장점

* 테스트를 통해 시간을 절약 할 수 있다.
* 테스트는 문제를 그저 식별하는 것이 아니라 예방 단계이다.
* 테스트가 코드를 더 매력적으로 만든다.
* 테스트는 팀이 함께 일하는 것을 돕는다.

<br>

<br>

## 첫 번째 테스트 작성하기

테스트 작성에는 많은 접근법이 존재하는데, [TDD](https://en.wikipedia.org/wiki/Test-driven_development)를 참고하자

### 버그 식별하기

`polls` 어플리케이션에는 바로 해결할 수 있는 약간의 버그가 있다. 바로 `Question.was_published_recently()` 메서드로 `Question`이 어제 게시된 경우 `True`를 반환하지만, `Question`의 `pub_date` 필드가 미래로 설정되어 있을 때도 그렇다. (음?) 여기서 바로 알아차려야하는데, 미래는 어제보다 다음이긴 하지만, 최근은 아니기 때문에 `False`를 반환하는 것이 옳다.

<br>

### 버그를 노출하는 테스트 만들기

> 여기서 나는 Django의 좋은 점을 느낀게, `tests.py`가 이미 만들어져있다. :thumbsup:

```python
# polls/tests.py
import datetime

from django.test import TestCase
from django.utils import timezone

from .models import Question


class QuestionModelTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() returns False for questions whose pub_date
        is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
```

<br>

### 테스트 실행

```bash
$ python manage.py test polls
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/mysite/polls/tests.py", line 18, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

결과는 예상한 대로 `FAILED (failures=1)` 로 테스트 케이스 1건 중 1개가 `F`로 나왔다.

그럼 어떤 과정으로 실행된 것일까?

1. `manage.py test polls`는 `polls` 어플리케이션에서 테스트를 탐색
2. `django.test.TestCase` 클래스의 서브 클래스를 찾는다.
3. 테스트 목적의 데이터베이스 생성
4. 이름이 `test`로 시작하는 테스트 메서드 탐색 (ex, test_was_published_recently_with_future_question)
5. `assertIs()` 메서드를 이용하여 `False`와 `future_question.was_published_recently()`의 반환값이 서로 같은지 확인한다.

<br>

### 버그 수정

무엇이 문제인지 알았으니 버그를 수정하자.

```python
# polls/models.py
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

    def __str__(self):
        return self.question_text

    def was_published_recently(self):
        now = timezone.now()
        return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

<br>

다시 테스트 실행

```bash
$ python manage.py test polls
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
Destroying test database for alias 'default'...
```

이처럼 테스트까지 완료되면 수정이 완료된 것이다. 하지만, 테스트 케이스가 여전히 빈약하니 과거, 최근, 미래를 테스트 케이스로 추가 작성

<br>

```python
class QuestionModelTests(TestCase):

    def test_was_published_recently_with_old_question(self):
        time = timezone.now() - datetime.timedelta(days=1, seconds=1)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)

    def test_was_published_recently_with_recent_question(self):
        time = timezone.now() + datetime.timedelta(days=23, minutes=59, seconds=59)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)

    def test_was_published_recently_with_future_question(self):
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
```

<br>

<br>

## 뷰 테스트

앞에서 작성했던 테스트와 동일한 현상으로 `polls` 어플리케이션은 뷰에도 버그가 있는데 바로 미래의 설문도 공개되어 보여진다는 것이다. 다시 말하면, `pub_date`가 미래로 작성되어 있는 경우에는 현재 노출되어선 안되고, 미래의 그 시점이 왔을 때 보여줘야한다.

<br>

### Django 테스트 클라이언트

Django는 뷰 레벨에서 코드와 상호 작용하는 사용자를 시뮬레이트하기 위해 테스트 클라이언트 클래스인 `Client`를 제공하는데, 이 테스트 클라이언트는 `tests.py` 또는 `shell` 에서 사용할 수 있다.

**1. Shell 을 이용한 방법**

```bash
$ python manage.py shell
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
```

여기서는 `tests.py`에서 테스트 케이스를 작성하던 것과 달리 하지 않았던 두 가지 일을 해야하는데, 첫 번째는 바로 `setup_test_environment()`를 이용한 테스트 환경을 구성하는 것이다.

`response.context`와 같은 `response`의 추가적인 속성을 사용할 수 있게 하기 위해서는 `setup_test_environment()`를 사용하여 템플릿 렌더러를 설치해야한다. 여기서 알아야할 것은 <u>이 메소드는 테스트 데이터베이스를 셋업하지 않는다.</u> 그렇기 때문에 테스트는 현재 사용중인 데이터베이스 위에서 돌게되며 결과는 데이터베이스에 이미 만들어져있는 질문들에 따라 조금씩 달라질 수 있다. 또한 `settings.py`의 `TIME_ZONE`이 올바르지 않으면 예상과 다른 결과가 나타날 수 있으니, 초기 설정을 꼭 확인하는 것이 중요하다. (나는 Asia/Seoul로 설정했다.)

```python
>>> from django.test import Client
>>> client = Client()
```

그 다음 테스트를 위한 테스트 클라이언트 클래스를 `import`한다. `TestCase`는 `tests.py`에서 사용할 것으로 여기서는 사용하지 않는다.

<br>

```python
>>> # on the other hand we should expect to find something at '/polls/'
>>> # we'll use 'reverse()' rather than a hardcoded URL
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#x27;s up?</a></li>\n    \n    </ul>\n\n'
>>> response.context['latest_question_list']
<QuerySet [<Question: What's up?>]>

```

<br>

### 뷰 개선하기

```python
# polls/views.py
class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]
```

`IndexView`를 보면 `Question.objects.order_by('-pub_date')[:5]`로 `-pub_data` 기준으로 내림차순된 5개의 데이터를 리턴한다. 아직도 뷰에서는 동일한 버그가 존재한다는 뜻이다.

다음과 같이 변경한다.

```python
# polls/views.py
from django.utils import timezone

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
    	return Question.objects.filter(
            pub_date__lte=timezone.now()
        ).order_by('-pub_date')[:5]
```

처음에 따라 작성하면서 이게 맞는건가라고 계속 생각했다. 사실 처음 보는 `pub_date__lte`가 있었기 때문인데, `pub_date__lte`에서 `__lte`는 `같거나 보다 작다` 라는 뜻으로 `timezone.now()` 보다 같거나 작은 데이터만 필터링하고 그 안에서 `pub_date`를 기준으로 내림차순된 5개의 리스트를 가져온 것이다.

**참고**

* [Django ORM - 불곰](https://brownbears.tistory.com/63)

**순서**

1. `filter(pub_date__lte=timezone.now())`
2. `order_by('-pub_date')[:5]`

<br>

### 새로운 ListView 테스트

```python
# polls/test.py
from django.urls import reverse

def create_question(question_text, days):
    """
    Create a question with the given `question_text` and published the
    given number of `days` offset to now (negative for questions published
    in the past, positive for questions that have yet to be published).
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)


class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """
        If no questions exist, an appropriate message is displayed.
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_past_question(self):
        """
        Questions with a pub_date in the past are displayed on the
        index page.
        """
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_future_question(self):
        """
        Questions with a pub_date in the future aren't displayed on
        the index page.
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_future_question_and_past_question(self):
        """
        Even if both past and future questions exist, only past questions
        are displayed.
        """
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_two_past_questions(self):
        """
        The questions index page may display multiple questions.
        """
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question 2.>', '<Question: Past question 1.>']
        )
```

굉장히 코드가 길다. 하나하나 살펴보자.

* `create_question(question_text, days)`
    * 테스트 과정 중 `poll`은 생성하는 부분에서 반복적으로 사용됨
* `QuestionIndexViewTests`
    * `test_no_questions`: 질문이 없는 경우 테스트
        * `response` 안에 `No polls are available`이 포함되어 있는지 확인
        * `response.context` 내 최근 질문 리스트가 빈 리스트인지 확인
    * `test_past_question` : 과거 질문이 있는 경우 테스트
        * 30일 전 (한달 전) 질문을 하나 생성하고 생성된 테스트가 `context` 안에 있는지 확인
    * `test_future_question`
        * 미래 질문 하나 생성 후 `IndexView`에서 보여주지 않는지 확인
    * `test_future_question_and_past_question`
    * `test_two_past_question`

<br>

### DetailView 테스트하기

현재 `DetailView`에 치명적인 버그가 있는데, 사용자가 URL을 알고 있거나 패턴을 통해 추측 가능하다면 미래의 설문까지 접근할 수 있는데, 이를 위해 `DetailView`에 제약 조건 추가

```python
# polls/views.py
class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'

    def get_queryset(self):
        return Question.objects.filter(pub_date__lte=timezone.now())
```



### 새로운 DetailView 테스트

```python
# polls/tests.py
class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        """
        The detail view of a question with a pub_date in the future
        returns a 404 not found.
        """
        future_question = create_question(question_text='Future question.', days=5)
        url = reverse('polls:detail', args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

    def test_past_question(self):
        """
        The detail view of a question with a pub_date in the past
        displays the question's text.
        """
        past_question = create_question(question_text='Past Question.', days=-5)
        url = reverse('polls:detail', args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
```

<br>

## 테스트는 많이 할 수록 좋다.

앞서 작성했던 테스트 코드를 보면 실제로 작성했던 `Model`, `View` 보다 훨씬 더 긴 테스트 코드들을 작성했는데, 어떻게 본다면 테스트 코드가 오히려 어플리케이션을 통제하고 있다고 볼 수도 있을 것이고, 어플리케이션이 점점 개발되어가면서 테스트 코드가 훨씬 더 많아질텐데 여기서 말하고자하는건 테스트 코드가 비대해지는걸 신경쓰지말라고 한다. 테스트 코드가 비대해질수록 프로그램을 개발하는 동안 계속해서 작동하고 계속해서 문제가 없다는 것을 수시로 보고해줄 것이기 때문이다. (내 의견) 하지만 때로는 테스트를 업데이트하고 테스트를 삭제하는 등의 행위가 있을 수 있는데, 이런 행위를 불필요한 행위로 여기지 말았으면 좋겠다. (우선 나부터 좀 지키자)

테스트하는 좋은 방법은 다음과 같다.

* 각 모델이나 뷰에 대한 별도의 `TestClass`
* 테스트하려는 각 조건 집합에 대해 분리된 테스트 방법
* 기능을 설명하는 테스트 메소드 이름 (나는 이게 가장 좋은 방법이라 생각한다)

<br>

### 추가 테스팅

앞서서 본 것들은 코드만 테스트했다고 볼 수도 있지만, 사실 여러가지 테스트 기법들이 있다. 그 중 하나가 `Selenium` 같은 브라우저 내 프레임 워크를 이용하여 HTML이 브라우저에서 실제로 렌더링되는 방식을 테스트할 수 있다. 실제로 Django에서는 `LiveServerTestCase`가 포함되어 있어 `Selenium`과 같은 도구와 쉽게 통합할 수 있게 해준다.

복잡한 어플리케이션을 사용하는 경우 [연속적으로 통합](https://en.wikipedia.org/wiki/Continuous_integration)하기 위해 모든 커밋마다 자동으로 테스트를 실행하여 품질 제어가 적어도 부분적으로 자동화되도록 할 수 있습니다. 어플리케이션에서 테스트되지 않은 부분을 테스트하는 좋은 방법은 `코드 커버리지`를 확인하는 것이다.