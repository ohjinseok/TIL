# ORM

```python
# object
class Article:
    def __init(self, id, title, content, author, created_at):
        self.id = id
        self.title = title
        self.content = content
        self.author = author
        self.created_at = created_at
        
a1 = Article(1, '제목', '내용', '오진석', '2019-02-07')
a2 = Article(2, '제목2', '내용2', '오진석2', '2019-02-07')

articles3 = [
    a1, a2
]

# list
articles[0][1]

# dict
articles[0]['title']

#obj
articles3[0].title      
```

data를 object 형태로 나타내면 method를 통해 편하게 관리할 수 있다.

data를 object로 계속해서 나타내는 것은 힘들기 때문에, ORM을 사용한다.

python orm으로 SQLAlchemy를 사용하겠음..

```python
from flask import Flask, render_template, request
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///blog2.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)
# SQLAlchemy 사용
```

### DB 수정 시 commit을 하는 이유

commit을 하기 전까지 transaction은 실제로 DB에서 이루어지지 않는다. commit을 하게 되면 쌓인 transaction들을 수행한다. 이 때, 사용되는 data record나 table은 lock을 걸어서 동시에 다수의 session에서 접근을 할 수 없게 한다. 그래서 데이터의 무결성을 유지한다.