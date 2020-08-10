## Git-ignore

`.gitignore` 파일은 쉽게 설명하면 `repository`에 불필요한 파일이 업로드 되는 것을 방지하기 위함인데, 이러한 파일들은 프로젝트마다 굉장히 다양하고 사용하는 언어마다 다르기 때문에 하나로 정의하기가 매우 어렵다. 하지만 대체적으로 다음과 같은 파일들을 많이 제외한다.

* Backup, Log, DB(ex, sqlite) 파일
* 계정 정보 혹은 보안의 이유로 업로드 되면 안되는 파일 
* 가상환경 파일
* 컴파일 된 파일

이렇듯 다양한 파일들이 있는데, 나는 여기서 [gitignore.io](https://www.toptal.com/developers/gitignore) 를 애용한다. 운영체제나 개발 환경 혹은 프로그래밍 언어 등을 적으면 그에 맞는 대표적으로 제외되어야하는 파일들을 묶어 `.gitignore` 파일을 만들어 준다.

<br>

### 사용 예시

**Python, Django, venv** 를 사용하는 **Django** 프로젝트를 토대로 `.gitignore` 파일을 생성하면 다음과 같다. 굉장히 내용이 긴데, 생략하여 그 중에 일부분만 보면 이렇다. Django의 경우 `*.log` / `db.sqlite3` 등이 있는데, 이처럼 로그 파일 혹은 DB 파일 같이 저장소(repository)에 올라가면 안되는 것들을 대략적으로 작성해준다.

```bash
# Created by https://www.toptal.com/developers/gitignore/api/python,venv,django
# Edit at https://www.toptal.com/developers/gitignore?templates=python,venv,vim,django

### Django ###
*.log
*.pot
*.pyc
__pycache__/
local_settings.py
db.sqlite3
db.sqlite3-journal
media

### Django.Python Stack ###
# Byte-compiled / optimized / DLL files
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
pip-wheel-metadata/
share/python-wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# ...

### venv ###
# Virtualenv
# http://iamzed.com/2009/05/07/a-primer-on-virtualenv/
[Bb]in
[Ii]nclude
[Ll]ib
[Ll]ib64
[Ll]ocal
[Ss]cripts
pyvenv.cfg
pip-selfcheck.json

# End of https://www.toptal.com/developers/gitignore/api/python,venv,vim,django
```

<br>

하지만 나는 여기에 `vscode`로 코드를 작성하는데, `.vscode` 폴더를 제외해야해서 아래와 같이 추가했다. `vscode`에서는 `.vscode/` 폴더에 `settings.json` 파일이 포함되어 프로젝트 내 설정들이 저장되어 있는데, 이를 제외했다.

```bash
### vscode ###
.vscode/
```

<br>

### 명령어

```bash
git rm -r cached .
git add .
git commit -m "apply .gitignore"
```



<br>

### 결론

기존 `gitignore` 없이 `git add .` 명령어를 사용하면 디렉토리 내 전체 파일을 추가하기 위해 `5K` 이상의 파일들이 `stage` 를 대기하고 있는 상태였는데, `gitignore` 를 설정한 이후에는 `20`개 가량으로 줄어 들어 어떤 파일에서 수정이 일어났는지 생성이 일어났는지 한 눈에 파악하기가 수월해졌다.

그리고 다른 장점으로는 실수로 `git add .`를 하더라도 `gitignore` 에 포함되어 있는 파일들은 `commit` 하더라도 올라가지 않는다. 이 시점을 확인 했던 때가 실수로 `vscode`에서 `stage`에 올려놓았던 파일 중 하나가 `.gitignore`에 포함되어 있음에도 불구하고 올라가 있던 적이 있었는데, 실수로 커밋 후 확인하니 올라가지 않은 것을 확인할 수 있었다.



기존에 `gitignore`이 없던 상황에서는 파일의 총 개수가 `5K`가 넘었는데, 현재는 `20`개 가량으로 줄어 업로드와 파일 관리가 더 편리해졌다.

<br>