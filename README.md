# django-tutorial

# Part 1

## Creating a project
```shell
 $ django-admin startproject mysite
 $ python manage.py migrate
 $ python manage.py runserver
```

## Creating models
```shell
 $ python manage.py startapp polls
```
 * Model class 생성 (Question, Choice)

## Database
 * django 1.8 에서 mysql-connector-python 이 지원하지 않으며, 다음 저장소에서 해당 내용의 패치 버전을 설치해야 이를 해결할 수 있다.
```shell
 $ pip install git+https://github.com/multiplay/mysql-connector-python
```
```python
DATABASES = {
    'default': {
        'ENGINE': 'mysql.connector.django',
        'NAME': 'mysite',
        'USER': 'root',
        'PASSWORD': '1234',
        'HOST': '',
        'PORT': '',
    }
}
```

## Activating models
```shell
 $ python manage.py makemigrations polls
 $ python manage.py sqlmigrate polls 0001
```
 * Model class 에서 정의한 내용은 Database schema, Python database-acess API 로 사용됨
 * 장고앱은 플러그 가능하고, 여러 프로젝트에서 사용할 수 있다.

## Model class 에 의한 동작
 * 사용하는 데이터베이스에 맞는 DDL 스크립트 생성
 * Table 이름은 app 이름과 모델이름의 조합으로 자동으로 생성되며, 사용자가 오버라이드 할수 있다.

## migrations 적용시 장고에서 동작
 * PK 는 자동 생성 (사용자 오버라이드 가능)
 * Foreign key 의 경의 "_id" 를 추가한다. (사용자 오버라이드 가능)
 * 사용자는 데이터베이스에 적합하도록 만들어진다. 예를들어 필드 자동증가의 경우 auto_increment(mysql), serial(postgresql) 등으로 적용된다.
 * 필드명은 " 또는 ' 로 쌓여진다.
 * sqlmigrate 명령은 데이터베이스에 마이그레이션을 실행하지 않고, 단지 SQL Script 를 출력한다.

## model 변경 철차
 * 모델 수정
```shell
 $ python manage.py makemigrations
 $ python manage.py migrate
```

## model 테스트
```shell
 $ python manage.py shell
```

# Part 2

## Admin site
 * Admin site 를 만드는것은 많은 창의성을 요구하지 않으며, 반복된 지루한 작업이다.
 * 장고에서는 모델에 대한 관리 인터페이스의 생성을 자동화 한다.
 * 사이트 관리자가 admin site 에서 서비스할 컨텐츠를 추가하면, public site 에서 해당 내용을 보여준다.
 * Admin site 는 사이트 방문자에 의해 사용될 수 없다.

## Creating an admin user
```shell
 $ python manage.py makemigrations polls
```

## Make the poll app modifiable in the admin
 * polls/admin.py 을 다음과 같이 수정한다.
```python
   from django.contrib import admin
   from .models import Question
   
   admin.site.register(Question)
```
 * Customize the admin form
```python
   fields = ['pub_date', 'question_text']
```
```python
   fieldsets = [
       (None,               {'fields': ['question_text']}),
       ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
   ]
```
 * Adding related objects
 * Customize the admin change list

## Customize the admin look and feel
 * Project directory (manage.py 가 존재하는) 에 tempalte/admin 디렉토리를 생성한다.  
 * 'django/contrib/admin/templates' 디렉토리에서 base_site.html 파일을 위에서 생성한 경로에 복사한다.

# Part 3
  
## Poll application views
 * Question "index" page
 * Question "detail" page
 * Question "results" page
 * Vote action
 
## Write your first view
 * polls/views.py 에 다음 코드를 입력한다.
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```
 * polls/urls.py 파일을 생성하고, 다음 코드를 입력한다.
```python
from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
``` 
 * mysite/urls.py 에 다음 코드를 추가한다.
```python
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', include(admin.site.urls)),
]
```
 * http://localhost:8000/polls/ 접속

## Writing more views
 * polls/views.py 에 다음코드를 삽입한다.
```python

def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

## Write views that actually do something
 * polls 디렉토리 하위에 templates/polls 디렉토리를 생성한다.
 * 위에서 생성한 디렉토리에 index.html 파일을 생성하고, 다음 내용을 추가한다.
```python
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```
 * polls/views.py 파일의 index() function 의 내용을 수정한다. (2가지 방법 예시)
```python
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = RequestContext(request, {
        'latest_question_list': latest_question_list,
    })
    return HttpResponse(template.render(context))
```
```python
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```
 * Rasing a 404 error
```python
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {"question": question})
```
```python
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```