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

```linux
> pipenv install djangorestframework

```

### Django 설정
* settings 파일의 `INSTALLED_APPS`에 `rest_framework`를 추가한다.

```linux
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```