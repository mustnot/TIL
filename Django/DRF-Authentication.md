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



Django 인증 시스템에 대해 공부해보려고 한다. 사용자 인증과 관련해서 기본적인 개념은 이해하고 있지만, 정확히 어떠한 방식으로 서버에서 작업하는지는 해본 적이 없었다.









## Djoser

Djoser는 Django 인증 시스템 라이브러리 중 하나로 REST API로 구현되었다. 계정과 관련된 등록, 로그인, 로그아웃, 암호 재설정 및 계정 활성화와 같은 기본 작업을 처리할 수 있는 API를 지원한다. 또한 사용자 지정 모델과 함께 작동하여 토큰 기반 인증에도 사용할 수 있다.

다음과 같은 엔드 포인트를 지원한다.

- `/users/`
- `/users/me/`
- `/users/confirm/`
- `/users/resend_activation/`
- `/users/set_password/`
- `/users/reset_password/`
- `/users/reset_password_confirm/`
- `/users/set_username/`
- `/users/reset_username/`
- `/users/reset_username_confirm/`
- `/token/login/`(토큰 기반 인증)
- `/token/logout/`(토큰 기반 인증)
- `/jwt/create/`(JSON웹 토큰 인증)
- `/jwt/refresh/`(JSON웹 토큰 인증)
- `/jwt/verify/`(JSON웹 토큰 인증)

