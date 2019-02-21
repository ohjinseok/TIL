# Database

## I. DB 관계

### (1) 1 : 1 관계

* 테이블 2개가 있을 때, 각각 테이블의 레코드와 레코드가 1 : 1
* ex) 부부관계, 사람과 주민 번호

### (2) 1 : N 관계

* 1 to many
* has many, belongs to
* ex) 학급(has many) - 학생들(belongs to) 1반 - 오진석, 강창모, 이철민 
* 게시글 - 댓글
* 유저 - 게시글

### (3) M : N 관계

* many to many
* ex) 수강 신청(수업, 학생) 
* 1 : N을 여러 개 가질 수 있다고 생각하면 된다

### (4) 관계 없음



## II. 게시판 댓글 프로젝트

Article과 comment 테이블이 있을 때, 1(Article) : N(comment) 관계를 맺는다.

이 때, N에서 속하는 1의 id를 **foreign key**로 갖는다. 

### (1) model 생성

```python
# models.py

class Comment(models.Model):
    content = models.TextField()
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
```

* 1 : N의 관계에서 1에 해당하는 Table의 model을 ForeignKey의 인자로 삼는다.
* on_delete는 ForeignKey에 해당하는 data가 기존 Table에서 삭제 됐을 때, 어떤 행동을 취할 지 정해주는 것이다.
* on_delete 
  * CASCADE : casacade deletes
  * PROTECT : prevent deletion
  * DO_NOTHING : take no action it can cause integrityerror
  * SET_NULL : set the foreignkey null, only possible if null is True
  * SET_DEFAULT : set the foreignkey to its default value default for the foreigneky must be set
  * SET() : set the foreignkey to the value passed to set()
* model 생성 후 migrate!

### (2) orm

```python
article = Article.objects.first()
comment = Comment(content="첫번째 글의 첫번째 댓글입니다.", article=article)
comment.save()
comment.content
'첫 번째 글의 첫 번째 댓글입니다.'
comment.article    
<1, 제목 테스트입니다!:ㅋㅋㅋㅋㅋㅋ>
    
Comment.objects.all()                                         
# <QuerySet [<Comment: Comment object (1)>]>

Comment.objects.first()                                       
# <Comment: Comment object (1)>

Comment.objects.first().article
# Article.objects.get(pk=Comment.objects.first().article_id) 와 같다.
# <1, 제목 테스트입니다!:ㅋㅋㅋㅋㅋㅋ>

Comment.objects.all()                           
# <QuerySet [<Comment: Comment object (1)>]>

# Article에서 댓글 조회
Comment.objects.filter(article_id=1).all()                             
# <QuerySet [<Comment: Comment object (1)>]>

Article.objects.first().comment_set.all()
# 위와 같은 코드
# <QuerySet [<Comment: Comment object (1)>]>
```

### (3) 댓글 조회

```python
# views.py
def comment(request, article_id):
    # 댓글을 작성한다.
    content = request.POST.get('content')
    comment = Comment(content=content, article_id=article_id)
    comment.save()
    return redirect('detail', article_id)

def detail(request, article_id):
    article = Article.objects.get(id=article_id)
    comments = Comment.objects.filter(article_id=article_id)
    context = {
        'article' : article,
        'comments' : comments,
    }
    return render(request, 'articles/detail.html', context)
```

```html
<!-- detail.html -->
<form action="{% url 'comment' article.id %}" method="POST">
    <input type="text" name="content"/>
    <input type="submit" value="Submit"/>
    {% csrf_token %}
</form>

<ul>
    {% for comment in comments %}
        <li>{{ comment.content }}</li>
    {% endfor %}
</ul>

<!-- improve -->
<ul>
    {% for comment in article.comment_set.all %}
    <li>comment.content</li>
    {% endfor %}
</ul>
```

### (4) improve

```python
# improve
class Comment(models.Model):
    content = models.TextField()
    article = models.ForeignKey(Article, on_delete=models.CASCADE, related_name="comments")
```

```html
<h1>게시글</h1>
<h3>{{ article.title }}</h3>
<p>{{ article.content }}</p>

<a href="{% url 'edit' article.id %}">수 정</a>
<a href="{% url 'delete' article.id %}">삭 제</a>
<a href="{% url 'index' %}">돌아가기</a>

<hr>

<h3>댓글</h3>

<form action="{% url 'comment' article.id %}" method="POST">
    <input type="text" name="content"/>
    <input type="submit" value="Submit"/>
    {% csrf_token %}
</form>

<ul>
    {% for comment in article.comments.all %}
    <!-- {% for comment in article.comments.all reversed %} 역순 -->
        <li>{{ comment.content }}</li>
    {% endfor %}
</ul>
```

* 조금 더 직관적으로 표현할 수 있다.



































