# Django REST Framework

Django REST Framework (이하 DRF) 란 [공식 홈페이지](https://www.django-rest-framework.org/)에 의하면 웹 API를 구축하기 위한 강력하고 유연한 툴킷이라 설명되어 있고, REST Framework를 사용하는 이유로 몇 가지를 나열하고 있다.

*  [Web browsable API](https://restframework.herokuapp.com/)는 개발자에게 유용성을 제공한다.
    * 처음에 사실 이해하지 못해 [자세한 내용](https://www.django-rest-framework.org/topics/browsable-api/)을 읽어보았고, Swagger와 유사한 기능으로 보였다. 이유로는 바로 `HTML` 형식의 요청이 왔을 경우 해당 API를 웹 페이지를 통해 어떤 메소드를 사용해야하고, 어떤 결과를 리턴하는지에 대해 안내해준다는 점인데 어떻게 보면 개발자로 하여금 API를 문서화하는 과정을 생략할 수 있는 기능을 제공해준다고 생각한다.
* [OAuth1a](https://www.django-rest-framework.org/api-guide/authentication/#django-rest-framework-oauth) 와 [OAuth2](https://www.django-rest-framework.org/api-guide/authentication/#django-oauth-toolkit) 등 다양한 인증 정책 (Authentication Policies)을 제공한다. 
* 직렬화([Serialization](https://www.django-rest-framework.org/api-guide/serializers/)) 기능을 제공하여 [ORM](https://www.django-rest-framework.org/api-guide/serializers#modelserializer) 와 [non-ORM](https://www.django-rest-framework.org/api-guide/serializers#serializers) 의 데이터 소스를 지원한다.
    * 직렬화라는 뜻이 어렵게 느껴질 수 있는데, 나는 다른 말로 형식화라고 생각했다. 형식을 변환해준다는 뜻으로 ORM을 쓰던, 쓰지않던 특정 데이터 소스가 들어왔을 때 이를 직렬화하여 사용자에게 보기 좋은 응답을 전달이 가능하다.

* 문서화와 커뮤니티 지원이 잘되어 있어 접근이 용이하다.

<br>

### Django REST Framework 설치

```bah
pip install djangorestframework
```

```python
# project/settings.py
INSTALL_APPS = [
    'rest_framework'
]
```

<br>

<br>

## Tutorial 1 - Serialization

### Setup

```bash
$ python3 -m venv env
$ source env/bin/activate

$ pip install django djangorestframework
$ pip install pygments  # We'll be using this for the code highlighting
# 나중에 한번 찾아봐야겠다.

$ djang-admin startproject tutorial
$ cd tutorial
$ python manage.py startapp snippets
```

<br>

### Creating a model to work with

> 튜토리얼 따라하다보면 정말 정석적으로 보이는 코드 스타일을 볼 수가 있는데, 간혹 기억하고 나중에 써먹고 싶은 것들이 종종 있다.

```python
from django.db import models
from pygments.lexers import get_all_lexers
from pygments.styles import get_all_styles

LEXERS = [item for item in get_all_lexers() if item[1]]
LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
STYLE_CHOICES = sorted([(item, item) for item in get_all_styles()])


class Snippet(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=100, blank=True, default='')
    code = models.TextField()
    linenos = models.BooleanField(default=False)
    language = models.CharField(choices=LANGUAGE_CHOICES, default='python', max_length=100)
    style = models.CharField(choices=STYLE_CHOICES, default='friendly', max_length=100)

    class Meta:
        ordering = ['created']
```

```bash
$ python manage.py makemigrations snippets
$ python manage.py sqlmigrate snippets 0001 # 이건 왜 안했지..?
$ python manage.py migrate
```

`Snippet` 모델인데, snippet은 사전적 의미는 짧은 단편(?) 이라고 하는데 몇 가지 찾아보니 상용구에도 많이 사용되는 단어로 보인다. 그래서 모델을 살펴보니 언어를 선택하고 언어에 대한 상용구를 만드는 모델 같이 보였다.

* `created` : 생성일자
* `title` : 제목 (빈칸 가능)
* `code` : 코드
* `linenos` : 이건 뭘까..
* `language` : 선택항목으로 `default`는 `python`이고 `pygments`에서 선택 가능한 언어를 선택할 수 있다.
* `style` : `style` 역시 `language`와 동일하게 `pygements`에 종속되어 있다.

`pygments`를 쓰는 이유를 봤더니 코드로 작성된 상용구를 `highlight` 하기 위한 라이브러리로 보인다.

<br>

### Creating a Serializer Class

> 여기서 serializers.py 라는 파일을 만들어서 별도로 serializers 를 관리하는데, 이건 좋은 방법인 것 같다. 나중에 참고하자.

```python
# serializers.py
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES


class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

    def create(self, validated_data):
        """
        Create and return a new `Snippet` instance, given the validated data.
        """
        return Snippet.objects.create(**validated_data)

    def update(self, instance, validated_data):
        """
        Update and return an existing `Snippet` instance, given the validated data.
        """
        instance.title = validated_data.get('title', instance.title)
        instance.code = validated_data.get('code', instance.code)
        instance.linenos = validated_data.get('linenos', instance.linenos)
        instance.language = validated_data.get('language', instance.language)
        instance.style = validated_data.get('style', instance.style)
        instance.save()
        return instance
```

지금까지 상태로 실행해보자

<br>

### Working with Serializers

```bash
$ python manage.py shell
>>> from snippets.models import Snippet
>>> from snippets.serializers import SnippetSerializer
>>> from rest_framework.renderers import JSONRenderer
>>> from rest_framework.parsers import JSONParser
>>>
>>> snippet = Snippet(code='foo = "bar"\n')
>>> snippet.save()
>>>
>>> snippet = Snippet(code='print("hello, world")\n')
>>> snippet.save()
```

이 때 느낀점이 있는데, 음? `views`를 만들지 않아도 되는건가? 라는 생각이 들었다. 나는 처음이지만 다른 블로그를 참고했을 때 `views.py`를 만들고 작업을 했었는데, 이렇게도 그냥 사용할 수 있다는걸 처음 알았다. (말이 좀 이상한데, 그냥 `views.py` 없이 사용할 수 있다는걸 처음 알았다.)

<br>

```bash
>>> serializer = SnippetSerializer(snippet)
>>> serializer.data
{'id': 2, 'title': '', 'code': 'print("hello, world")\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}
```

실행 결과를 보면 `code`를 제외하고 다른 필드들은 모두 `default` 값으로 들어간 것을 볼 수 있는데, 음 굳이 `model`을 직접 참조하지 않고 `serializer`만 이용해서도 데이터를 넣을 수 있구나라는 걸 알았다.

<br>

```bash
>>> content = JSONRenderer().render(serializer.data)
>>> content
b'{"id":2,"title":"","code":"print(\\"hello, world\\")\\n","linenos":false,"language":"python","style":"friendly"}'
```

`JSONRenderer()`를 이용해 `render`도 가능하다. (마치 `flask`의 `jsonify` 같다.) 이 기능은 `JSONParser`가 있는데 이는 기존에 `Python`에서 사용하던 것과 유사하다. 간단하게 보고 패스

<br>

```bash
>>> import io
>>> stream = io.BytesIO(content)
>>> data = JSONParser().parse(stream)
>>> data
{'id': 2, 'title': '', 'code': 'print("hello, world")\n', 'linenos': False, 'language': 'python', 'style': 'friendly'}
```

```bash
>>> serializer = SnippetSerializer(data=data)
>>> serializer.is_valid()
True
>>> serializer.validated_data
OrderedDict([('title', ''), ('code', 'print("hello, world")'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])
>>> serializer.save()
<Snippet: Snippet object (3)>
```

<br>

`Serializer`에 몇 가지 기능들이 있는데 그 중 하나가 `many=True` 로 하나만이 아니라 여러 개를 추출하고 추출된 결과를 `json = dict` 형태로 추출해준다. (하나만 있는 `Model`에서는 `many=True` 했다가 에러가 난 적이 있는데, 과연 이게 원인이었는지는 조금 더 살펴봐야겠다.)

```bash
>>> serializer = SnippetSerializer(Snippet.objects.all(), many=True)
>>> serializer.data
[OrderedDict([('id', 1), ('title', ''), ('code', 'foo = "bar"\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 2), ('title', ''), ('code', 'print("hello, world")\n'), ('linenos', False), ('language', 'python'), ('style', 'friendly')]), OrderedDict([('id', 3), ('title', ''), ('code', 'print("hello, world")'), ('linenos', False), ('language', 'python'), ('style', 'friendly')])]
```

<br>

### Using ModelSerializers

```python
# snippets/serializers.py
from rest_framework import serializers

class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = ['id', 'title', 'code', 'linenos', 'language', 'style']
```

`serializers`를 쓰는 방법 중에 `ModelSerializer` 방법이 있는데, 위에서 했던 방법은 앞서 만든 `Model`을 참조한 것이 아닌 `Model`처럼 만들어댄 `Serializer`였다. 자세히보면 `model`에서 작성했던 것을 중복해서 다시 사용하고 있는데, 나도 작성하면서 든 의문은 `model`에서 만들었던 걸 왜 여기서 또 반복적인 행위를 하나였다. 여기서 의문이 풀렸는데, 코드를 보면 위에 `serializers.Serializer`를 써서 만드는 방법보다 더 간결한 방법인 `serializers.ModelSerializer` 이다. 

`ModelSerializer`를 `meta class`에 `model`과 사용하고자 하는 `field`를 지정해주면 이를 이용해 `serializer` 를 생성한다. 여기서 `fields`를 리스트로 직접 입력했는데, `__all__`를 사용하는 방법도 있다.

<br>

### Writing regular Django views using our Serializer

> 💡 여기서 놀란 사실은 `method`를 구분 할 수 있는 방법을 이제 알았다는 사실과 나는 여태까지 이것보다 더 복잡하게 코드를 짜왔다는 사실이다.. (바똥멍)

```python
# snippets/views.py
from django.shortcuts import render

# Create your views here.
from django.http import HttpResponse, JsonResponse
from django.views.decorators.csrf import csrf_exempt
from rest_framework.parsers import JSONParser
from snippets.models import Snippet
from snippets.serializers import SnippetSerializer

@csrf_exempt
def snippet_list(request):
    """
    List all code snippets, or create a new snippet.
    """
    if request.method == 'GET':
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return JsonResponse(serializer.data, safe=False)

    elif request.method == 'POST':
        data = JSONParser().parse(request)
        serializer = SnippetSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data, status=201)
        return JsonResponse(serializer.errors, status=400)
    
@csrf_exempt
def snippet_detail(request, pk):
    """
    Retrieve, update or delete a code snippet.
    """
    try:
        snippet = Snippet.objects.get(pk=pk)
    except Snippet.DoesNotExist:
        return HttpResponse(status=404)

    if request.method == 'GET':
        serializer = SnippetSerializer(snippet)
        return JsonResponse(serializer.data)

    elif request.method == 'PUT':
        data = JSONParser().parse(request)
        serializer = SnippetSerializer(snippet, data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer.data)
        return JsonResponse(serializer.errors, status=400)

    elif request.method == 'DELETE':
        snippet.delete()
        return HttpResponse(status=204)
```

`!!!` `csrf_exempt`는 `csrf token`이 없는 경우에만 쓰도록 하자

여기서 당황했던건 `flask`랑 큰 차이가 없어 보였다는 점이다. `flask`에서도 똑같이 `method` 나누고 나눈걸 토대로 각기 다른 코드로 결과를 보여주는데, 여기서도 이렇게 쉽게 할 수 있다니... :sob: 그럼 이제 URL을 매핑해보자.

```python
# snippets/urls.py
from django.urls import path
from snippets import views

urlpatterns = [
    path('snippets/', views.snippet_list),
    path('snippets/<int:pk>/', views.snippet_detail),
]
```

```python
# urls.py
from django.urls import path, include

urlpatterns = [
    path('', include('snippets.urls'))
]
```

<br>

<br>

## Comments

굳이 `API Documents`를 볼 필요성이 없다면, 위와 같은 형식으로 `view` 를 만들어도 좋을 것 같다. 참고하자.

