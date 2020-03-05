---
layout: post
title: "DRF(Django Restful Framework)"
date: 2020-02-27
excerpt: "Django 프로젝트에 DRF 적용하기"
categories:
  - Django
tag:
  - django
  - drf
  - restful
---

## DRF

### DRF 설치
* `pipenv install djangorestframework`: 가상환경 매니저를 통해 local하게 설치하기 위해 pipenv를 사용하였다. 
   * global하게 설치하고 싶다면 `pip install`을 사용하면 된다.

```linux
> pipenv install djangorestframework

...(생략)
Success!
Updated Pipfile.lock (d65c54)!
Installing dependencies from Pipfile.lock (d65c54)
```

### Django 설정
* settings 파일의 `INSTALLED_APPS`에 `rest_framework`를 추가한다.

```linux
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

---
* http://raccoonyy.github.io/tag/django-rest-framework-3/index.html
* https://www.django-rest-framework.org/tutorial/quickstart/
