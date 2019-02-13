# Django

## 특징

### MTV

![1550022724683](C:\Users\student\AppData\Roaming\Typora\typora-user-images\1550022724683.png)

* Model : 데이터를 관리

* Template : 사용자가 보는 화면

* View : 중간 관리자

## 프로젝트

### 프로젝트 생성 및 환경 설정

* pyenv 설치

  pyenv는 현업에서 python version이 다 다를 가능성이 높기 때문에 version을 관리하기 쉽게 도와줌

  폴더별로 python version을 설정해줄 수 있음

```sh
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
exec "$SHELL"

git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
exec "$SHELL"
```

```sh
pyenv install 3.6.7
pyenv global 3.6.7
```

```sh
mkdir TEST
cd TEST
pyenv virtualenv 3.6.7 test-venv
# pyenv야 가상환경을 설정할건데 버전은 3.6.7로 해주고 이름은 test-venv로 붙여줘
pyenv local test-venv 
# 현재 폴더를 test-venv 가상환경으로 설정해줘
pip install django
# django를 설치하는데, 가상환경 안에만 존재한다.
django-admin startproject test .
# django project 시작
```

![1550025444769](assets/1550025444769.png)

* \__init__.py : 건드릴 일 없다.
* \__settings__.py, \____url.py__ : 주로 건드림
* wsgi.py : 배포할 때 건드림

```sh
python manage.py runserver 0.0.0.0:8080 # 서버 실행
```

settings.py 에서 `ALLOWED_HOSTS` 뒤에 배포주소(ex :  ` ['django-practice-gstjs.c9users.io']`)를 써줘야 에러가 안난다.

```python
# settings.py에서
TIME_ZONE = 'Asia/Seoul' # 시간 변경
LANGUAGE_CODE = 'ko-kr' # 언어 변경
```

### Application 제작

Django-project 안에 여러 가지 application이 있는 형태

```sh
python manage.py startapp pages # application 생성
```

application을 생성하고나서 settings.py에서 INSTALLED_APPS에 생성한 application을 추가해준다. ','를 추가해준다. (trailing comma)

view가 가장 중요하기 때문에 views.py 먼저 조진다.

```python
def index(request):
    return render(request, 'index.html')

# @app.route('/') -> urls.py
# def index():
#     return render_template('index.html')

# 위는 django 아래는 flask
```

```python
# urls.py
from django.contrib import admin
from django.urls import path
from pages import views

urlpatterns = [
    # path(url, 처리해야할 view)
    path('index/', views.index),
]
```

```python
# 변수를 전달하고 싶을 때
context = {'msg': "hello",'name': "John"}
return render(request, 'index.html', context)
```

```html
<h1>
    First Django!!
</h1>
<h2>
    {{msg}}
</h2>
<h3>
    {{name}}
</h3>
```

```python
# form 이용
word = request.GET.get('word')
```



legacy code

semantic versioning

![1550024480082](assets/1550024480082.png)

- breaking

  앞의 버전과 호환이 안되는 부분이 있다.

- feature

  이전 버전에 있는 것을 쓸 수 있지만, 새로 나온 것들이 있다.

- fix

  bug를 고친 것