# Django Tutorial Part 7

> Part 6와 이전에 자동으로 생성했던 관리자 사이트를 커스터마이징하는 데 초점을 맞춘 튜토리얼이다.

<br>

## 관리자 폼 커스터마이징

`Question` 모델을 `admin.site.register(Question)`에 등록하면서 Django는 디폴트 폼 표현을 구성할 수 있었는데, 관리 폼이 보이고 작동하는 방법을 커스터마이징하려는 경우가 있습니다. 이럴 때에는 객체를 등록 할 때 Django에 원하는 옵션을 알려주면 커스터마이징 할 수 있습니다.

수정 폼의 필드를 재정렬하여 작동하는 법을 확인하자.

```python
# polls/admin.py
from django.contrib import admin
from .models import Question

class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']
admin.site.register(Question, QuestionAdmin)
```

기능적으로 큰 차이는 없지만 결과 이미지를 보면 기존에는 `Question text` 다음 `Date published` 순서로 입력했다면, 이제는 `fields`에 입력한 순서대로 `Date published` 후 `Question text`를 입력하도록 변경되었다. 

튜토리얼에서 2개의 필드의 순서를 바꾸는 것이 어떻게보면 비효율적이고 가시적인 결과가 나타난 것 같지는 않지만, 만약에 수십 개의 필드가 있고, 특정 필드는 설정할 필요가 없을 경우에는 이런 방법으로 순서를 변경하는 것은 편리성 부분에서 중요한 포인트이다.

만약에 필드마다 카테고리가 있고, 카테고리로 필드를 묶고 싶으면 아래와 같은 형식으로도 관리자 폼을 만들 수 있다.

```python
# polls/admin.py
from django.contrib import admin
from .models import Question

class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]
admin.site.register(Question, QuestionAdmin)
```

<br>

<br>

## 관련된 객체 추가

`Question` 관리자 페이지는 만들었지만, `Choice` 와 관련된 설정은 관리자 페이지에서 할 수 없는데, 이를 해결하기 위해서 `admin` 페이지에 `Choice`도 추가했다. (나는 이미 이전에 귀찮아서 넣어버려놨다..)

```python
from django.contrib import admin

from .models import Choice, Question
# ...
admin.site.register(Choice)
```

하지만, 이대로 관리자 페이지에서 `Choice`를 선택하여 들어가면, 매우 비효율적이다. 그 이유는 `Question`이 `ForeignKey` 관계를 가진 객체이기 때문인데, Django에서는 `ForeignKey`를 가진 객체는 모두 `Add Another`이라는 링크를 갖게 된다. `Add Another` 링크가 수행하는 과정을 설명하면 다음과 같다.

1. `Add Question` 폼이 있는 팝업창이 나타난다.
2. 팝업창에 질문을 추가하고 `Save`를 클릭하면 Django는 질문을 데이터베이스에 저장한다.
3. 저장된 질문을 동적으로 보고 있었던 `Add Choice` 폼에 추가하여 질문을 선택할 수 있게한다.

이 과정이 매우 비효율적이라 하는데, 사실 나도 그렇게 생각했다. 그 이유 중 하나는 `Question`을 만들면서 `Choice`도 동시에 만들면 되지 않을까라고 생각했다. 왜냐면 `Question`을 만들고 다시 관리자 메인 화면으로 돌아와 다시 `Choice`를 선택하여 들어가서 그 안에서 질문을 선택한 후에야 `Choice`를 추가할 수 있는데, 이게 여간 비효율적인게 아니다. 만약 질문을 여러개 생성해야한다고 생각해보자. 얼마나 짜증날까..

그래서 해결하기 위한 방법은 `Question` 객체를 생성할 때 여러 개의 `Choice`를 추가할 수 있도록 하는 방법인데, 방법은 `Choice` 모델에 대해 `register()` 호출을 제거하고, `Question` 등록 코드를 다음과 같이 수정하면 된다.

```python
# polls/admin.py
from django.contrib import admin
from .models import Choice, Question

class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3

class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
# admin.site.register(Choice) 이를 제거
```

<br>

이런 다음 다시 Django 관리자 페이지에 들어가서 `Question` 페이지에 들어가면, `Choices`라는 카테고리(?)가 생성되어 기본적으로 3개의 리스트가 생겨 그 안에 선택 항목을 추가할 수 있도록 변경되었다.

만약 위와 같이 리스트 형태가 보기에 적합하지 않다면, `TabularInline`으로 변경 가능하다. 이는 테이블 형식의 라인뷰로 마치 표의 형태로 선택 항목을 추가가 가능하도록 작성되어 있다.

```python
class ChoiceInline(admin.TabularInline):
    # ...
```

<br>

<br>

## 관리자 변경 목록 (change list) 커스터 마이징

> 관리자 변경 목록 커스터마이징이라길래 변경 목록이 뭐지하고 한참을 찾아봤다..

기본적인 `Question` 변경 목록을 보면 아주 심플하게 (아주아주) `Question_text`만 리스트로 나열되어 있는데, 이렇게 되면 `pub_date`라던가, 최근 추가된 질문인지 눌러서 확인하는 수밖에 없다. 그럼 관리상에 이유로 매우 번거로운 작업들이 매번 반복될텐데 이를 방지하기 위해 커스터 마이징을 하려 한다.

기본적으로 `list_display`가 있어, 각 객체의 `PK` 값만 주로 보여주는데 `list_display`를 수정하여 보다 다양한 필드를 보여지게 할 수 있다.

```python
# polls/admin.py
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date')
```

<br>

이와 같이 `list_display = ('question_text', 'pub_date')`를 작성하면 (튜플로 작성) 기존 하나의 필드만 보여주던 것에서 `pub_date`가 추가되어 볼 수 있다. 하지만 더 나아가 `was_published_recently()`도 추가하고 싶기 때문에, 하나 더 추가하면 다음과 같다.

```python
# polls/admin.py
class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

<br>

단순히 메소드명만 추가하면 된다. 하지만 여기서 더 나아가  <u>모델에 몇 가지 속성을 부여</u>하여 `filter` 기능을 추가할 수 있다.

```python
class Question(models.Model):
    # ...
    def was_published_recently(self):
        now = timezone.now()
        return now - datetime.timedelta(days=1) <= self.pub_date <= now
    was_published_recently.admin_order_field = 'pub_date'
    was_published_recently.boolean = True
    was_published_recently.short_description = 'Published recently?'
```

```python
class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]
    list_filter = ['pub_date']
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

<br>

그리고 손쉽게 검색 기능도 만들 수 있다.

```python
class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]
    search_fields = ['question_text']
    list_filter = ['pub_date']
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

손쉽게 검색 기능을 만들었지만, `LIKE` 쿼리를 사용하기 대문에 검색 필드의 수를 적당한 수로 제한해야하는 필요성이 있다. (당연한 말이지만, 여러 필드에서 동시에 `LIKE` 쿼리는... )

<br>

<br>

## 관리자 Look & Feel 커스터마이징

> 도대체 룩앤필이 뭐야 이랬는데, 디자인을 말하는 것 같다. 사실 Django administarion은 정말로 별로다..

<br>

### 프로젝트 템플릿 커스터마이징

프로젝트 디렉토리 (`manage.py`를 포함하고 있는)에 `templates` 디렉토리를 만든다. (참고 : 템플릿은 Django가 액세스 가능한 모든 디렉토리 어디에서나 사용 가능하다)

설정 파일 (`settings.py`)을 열고 `DIRS` 옵션을 `TEMPLATES` 설정에 추가한다.

```python
# mysite/settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

<br>

그리고 `django/contrib/admin/templates/admin/base_site.html`을 복사하여 `mysite/templates/admin/base_site.html`로 복사하고 `'Django administration'`을 수정한다.

```html
<!-- mysite/templates/admin/base_sites.html -->
{% extends "admin/base.html" %}

{% block title %}{
    { title }} | {{ site_title|default:_('Django site admin') }}
{% endblock %}

{% block branding %}
<h1 id="site-name">
    <a href="{% url 'admin:index' %}">
        Polls Administration
    </a>
</h1>
{% endblock %}

{% block nav-global %}{% endblock %}

```



```python
# mysite/templates/admin/base_site.html
{% extends "admin/base.html" %}

{% block title %}{{ title }} | {{ site_title|default:_('Django site admin') }}{% endblock %}

{% block branding %}
<h1 id="site-name">
	<a href="{% url 'admin:index' %}">
		Polls administration') }}</a></h1>
{% endblock %}

{% block nav-global %}{% endblock %}

```

