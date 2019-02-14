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

```html
<!-- base.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>{% block title %}{% endblock %}</title>
</head>
<body>
    {% block body%}
    
    {% endblock %}
</body>
</html>
```

> django - base.html
>
> flask - layout.html

### Model 조작

- django ORM을 이용한 DB 조작

```python
# models.py
# Create your models here.
class Article(models.Model):
    title = models.TextField()
    content = models.TextField()
```

```sh
python manage.py makemirations
python manage.py migrate
python manage.py shell #django의 현 상태를 지닌 shell 띄우기
python manage.py sqlmigrate application table
# migration을 만드는 이유
# git의 commit과 비슷하다
# 버전을 쉽게 관리할 수 있다. 원하는 부분으로 롤백 할 수 있다.
```

```python
from articles.models import Article
a = Article(title="happy", content="hacking")
a.save()
Article.objects.all()
# 출력 format이 마음에 안 들 때, class에서 __repr__ 변경
# <QuerySet [제목: happy, 내용: hacking, 제목: 와 여기서도 글이 써진다!!, 내용: 쟝고 분노의 추적자]>

Article.objects.all()[0]
# 제목: happy, 내용: hacking

Article.objects.all()[1]
제목: 와 여기서도 글이 써진다!!, 내용: 쟝고 분노의 추적자

len(Article.objects.all())
# 2

for a in Article.objects.all():
...     print(a)
# Article object (1)
# Article object (2)
# 위를 변경하기 위해서는 __str__ 함수 정의

for a in Article.objects.all():
...     a
# 제목: happy, 내용: hacking
# 제목: 와 여기서도 글이 써진다!!, 내용: 쟝고 분노의 추적자

Article.objects.filter(title="happy").all()
# <QuerySet [제목: happy, 내용: hacking]>

Article.objects.filter(content="hacking").first()
# 제목: happy, 내용: hacking

Article.objects.filter(content="hacking").count()
# 1

Article.objects.get(pk=1)
# 제목: happy, 내용: hacking

Article.objects.get(id=1)
# 제목: happy, 내용: hacking

Article.objects.get(title="happy")
# 제목: happy, 내용: hacking

a = Article.objects.get(id=1)
a.content = "Thursday"
a.save()
Article.objects.get(id=1)
# 제목: happy, 내용: Thursday

Article.objects.order_by('id').all()
# <QuerySet [제목: happy, 내용: Thursday]>

Article.objects.create(title="hey", content="create")
# 제목: hey, 내용: create
        
Article.objects.all()
# <QuerySet [제목: happy, 내용: Thursday, 제목: title test, 내용: content test, 제목: hey, 내용: create]>
# save 방식으로 하는 것 추천

Article.objects.order_by('id').all()
# <QuerySet [제목: happy, 내용: Thursday, 제목: title test, 내용: content test, 제목: hey, 내용: create]>
Article.objects.order_by('-id').all()
# <QuerySet [제목: hey, 내용: create, 제목: title test, 내용: content test, 제목: happy, 내용: Thursday]>
```

### Admin 관리

```python
# admin.py
from .models import table

# Register your models here.
admin.site.register(table)
```

![1550126448342](assets/1550126448342.png)

```sh
python manage.py createsuperuser
```

```python
class JobAdmin(admin.ModelAdmin):
    list_display = ('name', 'job')
    
admin.site.register(Student, StudentAdmin)
```

![1550126413418](assets/1550126413418.png)

* descriptive programming (modern framework의 특징)





legacy code

semantic versioning

![1550024480082](assets/1550024480082.png)

- breaking

  앞의 버전과 호환이 안되는 부분이 있다.

- feature

  이전 버전에 있는 것을 쓸 수 있지만, 새로 나온 것들이 있다.

- fix

  bug를 고친 것