---
layout: post
title:  "Airbnb #2"
date:   2020-02-29
excerpt: "Academy|Nomad Coder Airbnb 강의 #2"
categories: 
 -Course
tag:
 - django
---

## 프로젝트 생성

* 프로젝트의 이름에 상관없이 config로 프로젝트를 생성한다.
   * 규모를 늘리거나 협업하는 등 구조화하기에 더 적합하다.
* 프로젝트가 생성되었으면 `config/` 폴더 안의 `config/`과 `manage.py`를 프로젝트 메인 폴더로 꺼내준다.

```linux
> django-admin startproject config
```

## Linter
* python은 컴파일 언어가 아닌 인터프리터 언어이다.
   * 컴파일 언어는 compiler가 프로그램이 시작되기 전에 에러를 잡아준다.
   * 파이썬은 runtime 에러라 시작되기 전에 잡아주지 않는다.
* Linter은 에러가 생길 부분을 미리 감지해주는 것이다.
   * python pep도 권장한다.

```linux
> pipenv install flake8 --dev
```

## Formater
* 코드를 Format 해주는 것이다.
   * 코드를 더 보기 좋게 해준다.

```linux
> pipenv install black --dev --pre
```

### .vscode/settings.json
* 앞의 설치한 것들은 이 파일을 보면 볼 수 있다.

## __init__.py
* python에게 `__init__.py`가 있는 폴더를 python에서 쓸 것이라고 알려주는 파일이다.
   * 일종의 import

## settings.py
* 장고의 코멘트에 연결된 링크들은 `settings.py`를 이해하는데 매우매우 좋은 정보들이다. 열심히 참고하고 검색하자!

## migrate
* migration: database에서, 하나의 상태에서 다른 상태(다른 데이터 유형)으로 바꾸는 것을 말한다.
   * 데이터 유형이 바뀔때마다 migration이 필요하다.
* `makemigrations`: 장고는 데이터 models를 확인하고 migration 파일을 생성한다.
* `migrate`: 장고를 사용하여 migration 파일로 데이터베이스를 업데이트한다.

```linux
> python manage.py makemigraions
> python manage.py migrate
```

## project를 계획하는 방법
* 다음의 기준을 가지고 project를 계획한다.
   * project: applicatin의 집합
   * application: function의 집합
* 처음은 application을 다음의 tip들을 유의하며 계획한다.
   * 하나의 주체를 기준으로 function들을 생각하고 application을 계획한다.
   * 한 문장으로 application을 설명할 수 있어야 한다.
   * 너무 많은 기능들을 한 애플리케이션에 집어 넣으면 안된다.(Divide and Conquer)
* 그리고 여러 기능이 모인 application들을 config에 import하여 합친다.

## Application 생성
* application의 이름은 반드시 복수형이 되어야 한다.

```linux
> django-admin startapp
```

## Application
* application 안의 파일들은 optional하지 않다.
   * 파일이름은 절대 변경하면 안된다.

* `admin.py`: Django의 admin 패널에 반영되는 것들을 정의하는 곳
* `apps.py`: configurations 파일
* `models.py`: data를 정의하는 곳. 원하는 database가 어떻게 구성되어 있는지 설명해야 하는 파일
* `tests.py`: 기능을 test하는 것들을 모아놓는 파일
* `views.py`: 사용자들이 보게되는 곳. html을 render(보여주는) function들을 만들어 넣는 파일
   * form을 보여주거나 upload 등
* `urls.py`(만들어 넣어야 하는 파일): 프로젝트 폴더(config)의 urls.py의 기능을 application 별로 분리하여 관리하기 위한 파일
   * 프로젝트 폴더의 urls.py에서 application의 urls.py를 import하여 사용한다.


---
- https://academy.nomadcoders.co/