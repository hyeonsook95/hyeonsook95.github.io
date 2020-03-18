---
layout: post
title:  "GrapnViz 설치"
date:   2020-03-18
excerpt: "Django GrapnViz를 사용해서 Django Mdoel Diagram 그리기"
categories: 
 - Django
tag:
 - django
 - graphviz
---

### 실습환경

|OS|Python|pip|
|:--|:--|:--|
|Windows 10|3.7.5|19.3.1|


## django-extensions 설치하기

1. pip를 이용해 django-extensions를 설치한다.
    * pipenv를 사용하고 있었으므로, pipenv로 진행했다.
    * `pipenv install django-extensions`

```linux
$ pipenv install django-extensions
```

2. Django에게 extensions를 사용할 것이라고 알려줘야 한다.
    * settings.py의 `INSTALLED_APPS`에 추가한다.

```python
INSTALLED_APPS = (
    ...
    'django_extensions',
)
```

여기까지하면 기본적으로 graph_models 기능을 이용해 dot 파일을 생성할 수 있다. 하지만 매우 보기 불편하므로 
예쁘게 보기 위해 graphviz 설치가 필요하다.

## graphviz 설치

그런데 `graphviz`를 사용하기 위해서는 Python과 연결시켜줄 `pygraphviz`나 `pydotplus`를 설치해야 한다.

1. `pygraphviz`나 `pydotplus`를 설치한다.
    * `pygraphviz`를 설치하려 했으나 계속된 오류로 `pydotplus`를 설치했다.

```linux
$ pipenv install pydotplus
```

2. `graphviz`를 설치한다.

```linux
$ pipenv install graphviz
```

3. settings.py에서 그룹모델을 지정해주자.
    * 꼭 지정하진 않아도 작동은 한다.

```python
GRAPH_MODELS = {
    "all_applications": True,
    "group_models": True,
}
```

## Model Diagram을 출력해보자.

```linux
$ python /manage.py graph_models -a -g -o test.jpg

```

---
* https://django-extensions.readthedocs.io/en/latest/installation_instructions.html
* https://django-extensions.readthedocs.io/en/latest/graph_models.html