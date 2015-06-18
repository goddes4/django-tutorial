# django-tutorial

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
