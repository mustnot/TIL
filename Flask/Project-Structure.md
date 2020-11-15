

## Project Structure

사실 프로젝트 구조를 어떻게 짜야하는지, 어떤 구조가 좋은지는 잘 모른다. 하지만, 지금까지 프로젝트를 진행하면서 생각했던 구조가 대략적으로 있는데, 그런 구조와 어떤 글을 레퍼런스 했는지 조금이나마 정리해보았다.

## My Structure

블루프린트(Blueprint)를 공부하면서 느낀 점은 메인 플라스크 프로젝트에 여러 개의 작은 프로젝트인 블루프린트 애플리케이션을 추가하는 느낌이라면, 각각의 프로젝트는 나중에 분리되더라도 하나의 역할을 잘 수행할 수 있어야 된다는 생각이 들었다.

그래서 각각의 애플리케이션 즉 블루프린트는 하나의 폴더인 `app` 폴더에 넣고 그 안에 모듈로서 나중에 추가하거나 제거하더라도 그 부분을 도려낼 수 있도록 구성하는 것이 좋다고 생각되었다.

보면 알 수 있듯이 모듈마다 `static`와 `templates` 폴더를 지니고 있으며, `views.py`를 포함하고 있어 분리 가능한 구조로 짜보았다. (물론! 이 구조가 100점짜리 정답이라고는 생각하지 않는다.)

```python
# example 2
Project
├── .venv
├── app
│   ├── __init__.py
│   ├── module_1
│   │   ├── __init__.py
│   │   ├── views.py
│   │   ├── static
│   │   └── templates
│   ├── module_2
│   │   ├── __init__.py
│   │   ├── views.py
│   │   ├── static
│   │   └── templates
├── config
│   ├── __init__.py
│   ├── main.py
│   └── db.py
├── migration
├── test
├── requirements.txt
└── manage.py
```

실제로도 레퍼런스 중 플라스크 공식 프로젝트 페이지에서 설명하고 있는 **Larger Applications** 페이지에서도 각 추가되는 애플리케이션마다 `static`, `templates` 폴더를 포함하고 있어, 나중에 분리되거나 다른 앱이 늘어나더라도 별도의 추가 작업이 크게 필요하지 않는 구조로 되어 있다. (물론 `config`나 이런건 신경써야할 필요가 있지만... 당연한 얘기는 패스)



## Reference
1. [Larger Applications - Flask Documentation (1.1.x)](https://flask.palletsprojects.com/en/1.1.x/patterns/packages/)
2. [Flask Web Development](https://www.oreilly.com/library/view/flask-web-development/9781491947586/ch07.html)
3. [How To Structure Large Flask Applications | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-structure-large-flask-applications)
