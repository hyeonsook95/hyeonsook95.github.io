---
layout: post
title:  "Airbnb #0 #1"
date:   2020-02-27
excerpt: "Academy|Nomad Coder Airbnb 강의 #0 #1"
categories: 
 -Course
tag:
 - django
 - pipenv
 - nomadcoders
---

## Flask

* Backend Framework
* 매우 미니널리스트하다.
* 매우 가벼워서 많은 기능을 제공하지 않는다.

## React

* Frontend Framework
* 모든 프론트엔드가 React를 필요로 하진 않는다.
* 사용자와의 상호작용이 필요한 경우에 적합하다.

## Django

* 많은 기능을 제공한다.
* 템플릿을 지원해주기 때문에 빠르게 개발할 수 있다.
* Django의 템플릿은 상호작용이 적은 컨텐츠 위주의 웹사이트에 적합하다.

## pipenv

* python의 공식 패키지 매니저는 pip이다.
* pip는 설치되는 모든 것은 globally하게 설치한다.
* 프로젝트에서 사용한 패키지와 python 버전들을 local하게 설치하고 사용하고 싶을 경우 pipenv를 사용해야 한다.
* pipenv는 pipfile을 생성한다.

### pipfile

* pipfile은 프로젝트의 의존 패키지들을 관리하고 프로젝트 정보를 저장한다.
* pipfile은 python 파일을 실행하거나 프로젝트를 새롭게 진행할 때 사용중인 패키지를 설치할 수 있도록 한다.

### pipenv 설치

```linux
> pip install pipenv --user pipenv
```

* 설치에 성공했다면 다음과 같은 화면이 나온다.

```linux
> pipenv

Usage: pipenv [OPTIONS] COMMAND [ARGS]...

Options:
  --where             Output project home information.      
  --venv              Output virtualenv information.        
  --py                Output Python interpreter information.
  --envs              Output Environment Variable options.  
  --rm                Remove the virtualenv.
  --bare              Minimal output.
  --completion        Output completion (to be eval'd).     
  --man               Display manpage.
  --support           Output diagnostic information for use in GitHub issues.
  --site-packages     Enable site-packages for the virtualenv.  [env var:
                      PIPENV_SITE_PACKAGES]
  --python TEXT       Specify which version of Python virtualenv should use.
  --three / --two     Use Python 3/2 when creating virtualenv.
  --clear             Clears caches (pipenv, pip, and pip-tools).  [env var:
                      PIPENV_CLEAR]
  -v, --verbose       Verbose mode.
  --pypi-mirror TEXT  Specify a PyPI mirror.
  --version           Show the version and exit.
  -h, --help          Show this message and exit.

...(후략)

```

### 독립된 environment 생성

* 독립된 enviroment을 만들어주려면 pipenv에게 python의 버전을 option을 통해 알려줘야 한다.

```linux
> pipenv --three

...(중략)

Successfully created virtual environment!
Virtualenv location: C:\Users\[UserName]\.virtualenvs\national_clone-wRnYxvBj
Creating a Pipfile for this project…
```

### environment 내부로 들어가기

* 위의 과정은 environment를 생성하기만 한 과정이다.
* 생성한 environment 안으로 들어가기 위해서는 `pipenv shell` 명령을 입력한다.

```linux
> pipenv shell

Launching subshell in virtual environment…
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

...(후략)
```

## Django

### Django 설치

* `pipenv install Django==[Version]`: 설치하고자하는 Django의 버전을 설치한다.

```linux
> pipenv install Django==2.2.5

Installing Django==2.2.5…
[==  ]to Pipfile's Installing... [packages]…
Installation Succeeded
Pipfile.lock not found, creating…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Success!

...(후략)
```

* pipfile을 확인해보면 Django가 설치된 것을 확인할 수 있다.

```
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]
django = "==2.2.5"

[requires]
python_version = "3.7"

```

---
- https://academy.nomadcoders.co/