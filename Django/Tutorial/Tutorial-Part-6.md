# Django Tutorial Part 6

> Part 5와 이어지는 내용으로 스타일 시트와 이미지를 추가하여 웹에서의 모양을 조금 더 아름답게 변경할 것이다.

<br>

먼저 서버에서 생성된 HTML을 제외하고, 웹 어플리케이션은 일반적으로 전체 웹 페이지를 렌더링하는 데 필요한 추가 파일을 제공해야합니다. Django에서는 이러한 파일을 "정적 파일"이라고 부르는데, 영문으로는 "Static Files" 라고 합니다. 주로 `/static` 폴더에 저장되어 `css, javascript` 등의 파일이 있습니다.

이러한 `static` 폴더를 위해 Django에는 `django.contrib.staticfiles`가 존재하는데, 이는 각 응용 프로그램의 정적 파일들을 프로덕션 환경에서 쉽게 제공할 수 있도록 단일 위치로 수집합니다.

<br>

## 앱의 모양과 느낌을 원하는 대로 변경하기

먼저 `polls` 디렉토리에 `static` 디렉토리를 생성하고, `polls/templates/` 안에 템플릿을 찾는 것과 비슷하게 정적 파일을 찾도록 합니다.

```bash
$ tree
├── __init__.py
├── admin.py
├── apps.py
├── migrations
├── models.py
├── static
│   └── polls
├── templates
│   └── polls
│       ├── detail.html
│       ├── index.html
│       └── results.html
├── tests.py
├── urls.py
└── views.py
```

몇 가지 생략되었지만, `templates/polls` 와 유사하게 `static/polls` 로 생성했습니다.

```css
/* polls/static/polls/style.css */
li a {
    color: green;
}
```

```html
<!-- polls/templates/polls/index.html -->
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
```

* `{% static %}` 템플릿 태그는 정적 파일의 절대 URL을 생성한다.
* 위 `css`는 질문의 링크가 녹색으로 변경 (튜토리얼에서는 장고 스타일이란다... ㅎ..)

<br>

## 배경 이미지 추가하기

다음은 이미지용 하위 디렉토리 만들기로 `polls/static/polls` 디렉토리에 `images` 서브 디렉토리를 만들어 이 디렉토리 안에 `background.gif`라는 이미지를 넣는다. (나는 jpg 파일로 했다.)

```css
/* polls/static/polls/style.css */
li a {
    color: green;
}

body {
    background: white
        url("images/background.jpg") no-repeat;
}
```

