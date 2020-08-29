---
layout: post
title:  "Windows10 에서 Docker 설치하기"
date:   2020-04-01
excerpt: "Install Docker on Windows10 Home"
categories: 
 - Docker
tag:
 - docker
 - windows 10
 - err
---

[Installation Failed: one prerequisite is ot fullfilled](.2020-04-01-install-docker-on-windows-home.md)

subicura님의 [초보를 위한 도커 안내서](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)를 실습하려고 도커를 설치하던 중 에러가 났다. `Docker Desktop requires Windows 10 Pro or Enterprise version 15063 to run.`

에러를 해결하기 위해 구글링을 하던 중 Carl-Johan Kihl님의 [Install Docker on Windows 10 Home](https://itnext.io/install-docker-on-windows-10-home-d8e621997c1d)를 보고 힌트를 얻었다.

## 원인

위 블로그에서는 이 에러가 일어난 이유는 도커가 Windows Pro 또는 Enterprise가 사용하는 Hyper-V와 Containers를 필요로 하기 때문이라고 한다.  

도커 커뮤니티의 [Unable to install docker on windows 10](https://forums.docker.com/t/unable-to-install-docker-on-windows-10/37542)와 [Installing Docker on Windows 10 Home](https://forums.docker.com/t/installing-docker-on-windows-10-home/11722/2) 에서도 Hyper-V와 Containers를 설치하라고 했으니, 설치해보자.

## 설치

### 배치파일이란?

* 순차적이고 반복된 동일한 작업 과정을 몇 개의 혹은 수십, 수백 개의 연관된 명령어를 하나의 파일로 집약하여 작업 과정을 수행하는 것.
* Window에서는 `.bat` 확장자를 가진 배치파일을 사용함.

### Hyper-V 설치

1. `InstallHyperV.bat` 파일을 만든다.
2. 다음 내용을 추가한다.

```powershell
pushd "%~dp0"

dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt

for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

del hyper-v.txt

Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```
3. 관리자 권한으로 실행한다.

### Containers 설치

1. `Installcontainers.bat` 파일을 만든다.
2. 다음 내용을 추가한다.

```powershell
pushd "%~dp0"

dir /b %SystemRoot%\servicing\Packages\*containers*.mum >containers.txt

for /f %%i in ('findstr /i . containers.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

del containers.txt

Dism /online /enable-feature /featurename:Containers -All /LimitAccess /ALL

pause

```
3. 관리자 권한으로 실행한다.

이 상태로 Docker를 설치하면! 실패한다. 두 프로그램을 설치해도 이 프로그램들이 Pro나 Enterprise라고 믿게 만들어야 작동하기 때문이다..

### Windows 레지스트리 편집하기

윈도우 레지스트리는 윈도우 운영 체제의 데이터베이스로 HW, OS SW, 사용자 등의 정보와 설정들이 들어있다. 여기서 Windows가 Pro라고 속여야 한다.

1. 윈도우 검색창에 `레지스트리 편집기`라고 치고 편집기를 연다(OR `Windows + R` 키로 cmd창에 `regedit`이라고 쳐도 열린다).

그럼 아래와 같은 창이 나온다.

![레지스트리 편집기](../images/posts/regedit_edit.png)

2. `\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion`에 들어간다.

찾기 힘들면 주소창에 복사하자.

![레지스트리 편집기](../images/posts/regedit_edit02.png)

3. 이제 `EditionID` 를 찾아서 `오른쪽 버튼>수정`을 누르고, 값 데이터를 `Professional`로 바꿔준다.

![레지스트리 편집기](../images/posts/regedit_edit03.png)

### Docker 설치

이제 Docker를 설치하면 된다. 이 값은 일시적으로 바뀐 것이라 재부팅하면 값이 리셋되니 주의해야 한다.


---

* [윈도우 BATCH 파일 작성 팁](https://jangpd007.tistory.com/163)
* [위키백과 윈도우 레지스트리](https://ko.wikipedia.org/wiki/%EC%9C%88%EB%8F%84%EC%9A%B0_%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC)

* [Install Docker on Windows 10 Home](https://itnext.io/install-docker-on-windows-10-home-d8e621997c1d)
* [Unable to install docker on windows 10](https://forums.docker.com/t/unable-to-install-docker-on-windows-10/37542)
* [Installing Docker on Windows 10 Home](https://forums.docker.com/t/installing-docker-on-windows-10-home/11722/2)