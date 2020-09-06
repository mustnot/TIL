# Django Authentication

> 지금까지 Flask로 API를 개발하면서, 사용자 인증에 대해 기본적인 개념은 이해하고 있었지만 정확히 어떠한 과정으로 서버를 통해 사용자가 등록되고 세션이 유지되는지 알지 못하는 부분이 많다. 그래서 이번에 Django REST Framework를 공부하면서 사용자 인증에 대해 공부해보려고 한다.

<br>

## Custom User Model

먼저, 유저 모델을 생성한다.

```python
# api/models.py
from django.db import models
from django.contrib.auth.models import AbstractBaseUser, PermissionsMixin, BaseUserManager


class UserAccountManager(BaseUserManager):
    def create_user(self, email, name, password=None):
        if not email:
            raise ValueError("User must have an email address")

        email = self.normalize_email(email)
        user = self.model(email=email, name=name)

        user.set_password(password)
        user.save()

        return user

class UserAccount(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(max_length=255, unique=True)
    name = models.CharField(max_length=255)
    is_admin = models.BooleanField(default=False)
    is_active = models.BooleanField(default=True)

    objects = UserAccountManager()

    USERNAME_FIELD = "email"
    REQUIRED_FIELDS = [
        "name"
    ]

    def get_full_name(self):
        return self.name

    def get_short_name(self):
        return self.name

    def __str__(self):
        return self.email
```

2개의 모델이 만들어졌다.

### UserAccount

`UserAccount` 모델은 `AbstractBaseUser`와 `PermissionsMixin` 두 개의 클래스를 상속받았다. 각각은 다음과 같은 역할을 한다.

* `AbstractBaseUser`을 상속하여 User 커스텀 모델을 만들면 로그인 아이디로 이메일 주소를 사용하거나 Django 로그인 절차가 아닌 다른 인증 절차를 직접 구현할 수 있다.
  * 단점으로 시스템 운영 중에 사용자 모델을 변경하는 것이 매우 어려워, 이미 운영 중인 경우에는 기존 모델을 사용하자.
* `PermissionsMixin`은 사용자와 관리자 권한을 분리할 수 있다.

모델을 살펴보면, 생성된 모델은  `email` 을 계정으로 한다는 것을 볼 수 있다.

<br>

### UserAccountManager

`UserAccountManager` 은  `BaseUserManager`를 상속받아 만들어졌는데, 주로 User를 생성할 때 사용하는 클래스로 두 가지 함수를 갖고 있다.

* `create_user()` 
* `create_superuser()`

두 가지 유저를 생성할 수 있는 것은 매우 좋은 것 같다. 이전에 프로젝트할 때에는 유저 권한을 분리하기 위해 각각의 함수를 권한과 함께 복잡하게 작성했었는데 이를 단순하게 만들어준다.

<br>

<br>

## Djoser Setup

> ℹ️ Django에서 Authentication 과 관련된 라이브러리가 다양하게 있지만, 보고 있는 예제 영상에서는 Djoser를 사용하고 있어 사용해보고자 한다. (실무에서도 사용될지는 모르겠지만...)

Djoser는 Django 인증 시스템 라이브러리 중 하나로 REST API로 구현되었다. 계정과 관련된 등록, 로그인, 로그아웃, 암호 재설정 및 계정 활성화와 같은 기본 작업을 처리할 수 있는 API를 지원한다. 또한 사용자 지정 모델과 함께 작동하여 토큰 기반 인증에도 사용할 수 있다.

설치 순서 :

```bash
$ pipenv install djoser
# if using JWT authentication
$ pipenv install djangorestframework_simplejwt
# if using third party based authentication e.g. facebook
$ pipenv install social-auth-app-django
```

설정 방법 :

```python
# settings.py
INSTALLED_APPS = (
		'django.contrib.auth',
  	# ...
  	'rest_framework',
  	'djoser'
  	# ...
)
```

```python
# urls.py
urlpatterns = [
  # ...
  url(r'^auth/', include('djoser.urls'))
]
```

이와 같이 설정을 모두 마치면 Djoser에서는 다음과 같은 URL을 사용할 수 있다. 

- `/users/`
- `/users/me/`
- `/users/confirm/`
- `/users/resend_activation/`
- `/users/set_password/`
- `/users/reset_password/`

* ....

> 더 많으니 [Djoser - Getting started](https://djoser.readthedocs.io/en/latest/getting_started.html)를 참조

<br>

