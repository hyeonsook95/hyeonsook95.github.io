---
layout: post
title:  "Docker로 Django 개발하고 배포하기"
date:   2020-03-27
excerpt: "Docker란? Django와 연결하기"
categories: 
 - Django
tag:
 - django
 - docker
 - aws
---

## Docker란?
* 애플리케이션을 신속하게 구축, 테스트 및 배포할 수 있는 소프트웨어 플랫폼
* 소프트웨어를 컨테이너라는 표준화된 유닛으로 패키징하며, 이 컨테이너에는 라이브러리, 시스템 도구, 코드, 런타임 등 소프트웨어를 실행하는 데 필요한 모든 것이 포함되어 있음.
* 소프트웨어를 한 컴퓨팅 환경에서 다른 컴퓨팅 환경으로 이동하면서 안정적으로 실행하기 위한 방법.

    > 도커 컨테이너는 일종의 소프트웨어를 소프트웨어의 실행에 필요한 모든 것을 포함하는 완전한 파일 시스템 안에 감싼다. 여기에는 코드, 런타임, 시스템 도구, 시스템 라이브러리 등 서버에 설치되는 무엇이든 아우른다. 이는 실행 중인 환경에 관계 없이 언제나 동일하게 실행될 것을 보증한다.

### 컨테이너란?
* 운영체제 위에 올려진 App 가상화
* 애플리케이션, 실행에 필요한 라이브러리, 바이너리, 구성 파일 등을 묶은 패키지

### 이미지란?
* 컨테이너를 구성할 파일과 설정값 등을 포함하고 있는 일종의 클래스
* 클래스로 인스턴스를 만들듯이 이미지로 컨테이너들을 만들어 사용함

## Django 프로젝트에 Docker 설치
* Docker는 기본적으로 리눅스 컨테이너 기술이므로 macOS나 windows에 설치할 경우 가상머신에 설치됨.
* AWS Linux에 그대로 올릴 수 있음.

### Windows 용 Docker 설치
* https://docs.docker.com/docker-for-windows/
* 하니까 에러남
    * Docker Desktop requires Windows 10 Pro or Enterprise version 15063 to run.
    * https://luckygg.tistory.com/165

---
* http://www.itworld.co.kr/t/62077/VDI/103469
* https://tech.peoplefund.co.kr/2017/04/03/what-is-docker-ko.html
* https://nachwon.github.io/django-deploy-8-docker/
* https://aws.amazon.com/ko/docker/

* https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html
* https://chohyeonkeun.github.io/2019/06/21/190621-django-docker-elastic-beanstalk/