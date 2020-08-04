## Django Tutorial Part 1

### 데이터 베이스 설치

```python
# mysite/settings.py
# ...
# Database
# https://docs.djangoproject.com/en/3.0/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
# ...
```

Django 프로젝트는 기본적으로 **SQLite**를 사용하도록 구성되어 있다. 만약 지정된 데이터베이스가 있다면 위에서 default 설정을 변경해주면 된다. 

* ENGINE : `django.db.backends.sqlite3` 외에도 `postgresql`, `mysql`, `oracle` 등이 있다. 
* NAME : 백엔드가 **SQLite**인 경우에만 **NAME**을 이용하며 이 외에는 **USER**, **PASSWORD**, **HOST** 같은 추가 설정이 반드시 필요하다.

> 우선 SQLite3로 연습하고 이후에는 MySQL로 변경해서 연습해보자 !

<br>

`mysite/settings.py`를 조금 더 살펴보면 `INSTALLED_APPS`에 여러 앱들이 포함되어 있는데, 명칭 그대로의 역할인 것 같아 나중에 직접 사용할 일이 있을 때 자세히 살펴보아야겠다.

* django.contrib.admin
* django.contrib.auth
* django.contrib.contenttypes
* django.contrib.sessions
* django.contrib.messages
* django.contrib.staticfiles

이런 애플리케이션들 중 일부는 최소 하나 혹은 그 이상의 데이터베이스 내 테이블을 사용하는데, 그러기 위해서는 사용하도록한 데이터베이스에 테이블을 미리 만들 필요가 있고 아래 명령어를 통해 실행 가능하다.

<br>

```bash
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permi
```

<br>



### 모델 만들기 및 활성화

```python
# polls/models.py
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

```python
# mysite/settings.py
INSTALLED_APPS = [
    'polls.apps.PollsConfig',			# add here
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

<br>

`polls/models.py`는 일반적인 `DB` 모델과 동일한 형태를 띄는 것 같다. 다만 쿼리를 이용해서 모델(테이블)을 만드는 것이 아닌 단지 코드로만 작성할 수 있다는 점이 장점으로 생각된다. 이후 `mysite/settings.py`의 `INSTALLED_APPS`에 `polls`라는 앱의 설정을 추가하여 `polls` 앱을 추가한다.

> INSTALLED_APPS는 처음에 라이브러리 같은 건줄 알았는데, 지금보니 django app들을 추가하고 삭제하는 것도 할 수 있어, django 프로젝트에서 새로운 app을 추가하거나 패치하기 전에 꼭 확인해야하는 코드 중 하나로 보인다.

<br>

`makemigration`은 `migration`과 유사한 기능으로 보인다. `makemigration`을 실행하는 이유는 모델에 대한 변경 사실과 변경 사항을 `migration` 으로 저장하고 싶다는 것을 Django에게 알려주기 위함이다.

```bash
$ python manage.py makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice    
```

<br>

`sqlmigrate` 는 `migration` 이름을 인수로 받아 실행하는데, 이 때 인수는 앞서 실행한 `makemigrations`의 결과에서 리턴한 `0001_initial.py`와 연관이 높은 것으로 생각된다. 실행 결과를 살펴보면 앞서 `models.py`에 작성한 코드를 통해 DB에 테이블을 생성하는 것을 볼 수 있다.

```bash
$ python manage.py sqlmigrate polls 0001
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" (
	"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
	"question_text" varchar(200) NOT NULL, 
	"pub_date" datetime NOT NULL
    );
--
-- Create model Choice
--
CREATE TABLE "polls_choice" (
	"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
	"choice_text" varchar(200) NOT NULL, 
	"votes" integer NOT NULL, 
	"question_id" integer NOT NULL REFERENCES 
	"polls_question" ("id") DEFERRABLE INITIALLY DEFERRED
	);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
```

```bash
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK
```

<br>

과정에 대해 종합하면

1. `models.py`에서 모델을 변경한다.
2. `python manage.py makemigrations`를 통해 변경사항에 대한 마이그레이션을 만든다.
3. `python manage.py sqlmigration polls 0001` DB에 대한 변경사항이 있을 경우 사용 (때로는 생략)
4. `python manage.py migrate` 명령어를 통해 변경사항을 데이터베이스에 적용