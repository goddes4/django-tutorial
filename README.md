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
 
