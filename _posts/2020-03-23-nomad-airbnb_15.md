---
layout: post
title:  "Airbnb #26"
date:   2020-03-23
excerpt: "Academy|Nomad Coder Airbnb 강의 #26"
categories: 
 - Course
tag:
 - django
 - aws
 - nomadcoders
---

~26.5

### 실습환경

|OS|Python|pip|
|:--|:--|:--|
|Windows 10|3.7.5|19.3.1|

* AWS 실습환경

|OS|DB|
|:--|:--|
|Ubuntu|Postgresql 9.6|


## Elastic Beanstalk
* ec2 인스턴스를 자동으로 생성해줌. 일종의 단축키.
    * eb로 직접적인 서버관리 및 수정은 불가능함(원한다면 SSH를 설정할 것).
* ec2/로드밸랜서/db/로직 등을 만듦.

### Install AWS CLI
* `pip install awsebcli`

### eb 초기화
* `eb init`

### IAM
* eb에게 내가 누군지 알려야 함.
    * 새로운 유저를 만들어 권한을 설정함.
    * eb에 id와 key로 접속.
        * key 값은 다시는 볼 수 없으므로 id/key 생성 후 따로 저장해야 함.

### .ebextensions 생성
* 서버가 작동하기 위한 정보를 적어넣기 위한 파일
    * ex) 매번 서버를 올리면서 마이그레이션할 때, 처음으로 AWS으로 서버를 배포할 때, 슈퍼유저 만들기 등.
* `.ebextensions/django.config` 파일 생성

```python
option_settings:
  aws:elasticbeanstalk:container:python:
    WSGIPath: ebdjango/wsgi.py
```

* `project_folder/wsgi.py`
    * wsgi는 django가 앱을 배포하는 하나의 방법
    * 서버를 작동시키면 eb는 `python manage.py runser`가 아닌 `wsgi`로 서버를 작동시킴.
* eb가 wsgi를 작동시키기 위해서는 `wsgi.py` 파일이 어디있는지 알려줘야 함.
    * .ebextensions에서 설정하면 eb는 wsgi가 어딨는지 알 수 있음.

### eb 가상환경 생성
* `eb create [eb_name]`
    * eb는 eb가 create된 곳의 모든 파일을 압축해서 가져감.
    * aws s3로 upload함.
    * 그리고 쉬운 인스턴스를 줌.
* 가상환경을 test용과 production으로 2개를 만들어라.
    * 테스팅 서버에 먼저 올리고, 체크해본 후 작동이 잘 되면 실제 배포하는 걸 추천함.    
* **err** wsgi 파일을 찾을 수 없음
    * eb는 git의 commit되어 있는 것까지 가져감.
    * commit되지 않은 파일은 가져가지 않음.
    * 수정하고 반드시 commit할 것.

### eb 배포
* `eb deploy`

## 서버 설정

* eb를 배포했지만, `No module ~` 오류가 뜸.
* 서버는 아직 django와 같은 lib들이 설치되지않은 깨끗한 상태이기 때문.

### requirements.txt
* 프로젝트에 따라 python 패키지를 설치하기 위해 필요한 파일.
* python 프로젝트가 있다면 반드시 requirements.txt 파일을 봐야 함.
* 기본적으로 eb는 파일 중에서 requirement.txt를 찾음.
* eb가 requriements.txt 파일을 찾으면 그 안의 모듈들을 자동으로 설치해줌.

* pip를 통해 requirements 파일을 생성.

```linux

> pip freeze > requirements.txt

```

OR

* pipenv로 requirements 파일을 생성.

```linux

> pipenv install pipenv-to-requirements
> pipenv_to_requirements -f

```

## DB 설정
* db.sqlite3는 `python manage.py runser`를 했을 때 자동으로 생성됨.
* wsgi로 실행됐을 때는 db.sqlite3가 생성되지 않음.

### AWS RDS(Relation Database Service)
* 데이터베이스틑 인스턴스와 함께 있으면 안됨.
    * `eb deploy`할 때마다 app이 죽기 때문
* AWS에서 DB를 EB 설정에서 분리되도록 해줌.

### Django Postgres 설정
* `psycopg`: python에서 지원하는 postgres 어댑터
* `requirements.txt`

```txt

psycopg==2.8.4

```

* settings.py
* 하지만 **코드 안에 user name, password와 같은 중요한 정보를 넣는 건 위험**
* `.env`에서 가져와서 사요하는 것처럼 환경변수로 설정해야 함.

```python

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "HOST": "[AWS RDS의 ENDPOIONT]",
        "NAME": "[AWS RDS에서 만든 DB 이름]",
        "USER": "[AWS RDS에서 만든 USER 이름]",
        "PASSWORD": "[AWS RDS에서 만든 PASSWORD]",
        "PORT": "[AWS RDS PORT 번호]",
    }
}

```
 
### EB 환경변수
* ElasticBeanstalk의 Software Modify
* 환경변수 추가후 Django `settings.py` 수정

```python

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "HOST": os.environ.get("RDS_HOST"),
        "NAME": os.environ.get("RDS_NAME"),
        "USER": os.environ.get("RDS_USER"),
        "PASSWORD": os.environ.get("RDS_PASSWORD"),
        "PORT": os.environ.get("RDS_PORT"),
    }
}

```

### packages.config
* `.ebextensions/packages.config` 파일을 만듦.
* eb는 어떤 소프트웨어를 설치해야 하는지 모름. 알려줘야 함.
    * [공식문서](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers-ec2.html)에서 관련 사항을 자세히 볼 수 있음

```config

packages:
  yum:
    postgresql[AWS RDS에서 설정한 DB version]-devel: []

```

### RDS DB와 EB 연결
* RDS DB의 Security group rules 설정해야 함.
    * Security group은 AWS에서 서로 통신할 수 있는 권한을 부여받은 것들을 모아놓은 것.
    * RDS와 통신하고 싶다면 Security group에 속해있어야 한다는 말.
    * Security group에 생성한 EB 인스턴스를 넣으면 통신할 수 있게 됨.

---
* https://academy.nomadcoders.co/