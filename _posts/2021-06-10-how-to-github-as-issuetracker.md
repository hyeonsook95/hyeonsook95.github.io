---
layout: post
title: "Github로 이슈 트래킹하기"
date: 2021-06-10
tag:
 - github
---

깃헙을 사용한지는 꽤 오래되었지만, 혼자 저장소로 사용한다는 느낌이어서 좀 더 관리를 하자 이런 느낌이 없었다. 그러던 중 2, 3주 전에 팀에 개발자 한 분이 들어오면서 깃을 잘 관리하고자 **GitHub flow**로 커밋을 관리하기로 했다. 

## GitHub flow 란?

깃을 통한 버전 관리 방법 중의 하나이다. [깃헙 가이드](https://guides.github.com/introduction/flow/)에서도 자세하게 설명하고 있다.

![image](https://user-images.githubusercontent.com/23014708/121540934-d3cc5200-ca41-11eb-8e10-f5fd864a47d4.png)

새로운 기능이나 버그를 해결하기 위해 브랜치를 생성하여 해당 브런치에서 작업하는 것과 master에 머지되고 푸쉬되면 즉시 배포되는 것이 핵심이다.

프로젝트의 규모가 작고, 이미 사용중인 GitHub을 활용하는 게 좋을 것 같아서 선택했다.

## 이슈 트래커란?

이슈(기능 추가, 버그) 목록을 관리하고 유지보수하는 컴퓨터 소프트웨어이다. 

디자이너분과 작업할 때는 원래 사용하시던 Trello를 사용해서 할 일을 관리했었다. 그런데 프로젝트 기간이 길어질 수록 원래 코드를 유지보수해야 하는 기간이 늘었다.

유지보수를 하면서 해결한 문제나 추가한 기능을 추적할 수 있었으면 좋겠다는 생각이 들었고, 이슈 트래커를 찾게 되었다.

마침 깃헙에서 사용할 수 있었기에 플로우를 적용하며 같이 적용하였다.

## 깃헙(GitHub) 저장소에서 수정하기

### Label 추가

해당 저장소의 `Issues`에서 `Labels`를 클릭한다.

![image](https://user-images.githubusercontent.com/23014708/121545167-3bd06780-ca45-11eb-844a-26e89e2cc199.png)

그 후 라벨을 커스텀하면 된다.

나는 종류로 `bug/documentation/enhancement/question`과 우선순위로 `major/minor` 를 사용하기 위해 아래와 같이 커스텀했다.

![image](https://user-images.githubusercontent.com/23014708/121545852-d630ab00-ca45-11eb-9d5d-b39bd2e4fa00.png)

그 후 이슈를 생성할 때, 라벨을 선택해주면 된다.

![image](https://user-images.githubusercontent.com/23014708/121546694-7686cf80-ca46-11eb-8f5d-d44fcad28501.png)


그러면 이슈를 볼 때 아래와 같이 라벨이 붙은 이슈를 볼 수 있다.

![image](https://user-images.githubusercontent.com/23014708/121547025-bc439800-ca46-11eb-8462-0869a00e2f4b.png)

### PR 할 때, 이슈 완료하기

PR을 하면서 `#이슈번호` 를 해주면, 

![image](https://user-images.githubusercontent.com/23014708/121547538-307e3b80-ca47-11eb-8e6c-72484e2b4332.png)

닫힌 이슈를 확인 할 수 있다.

![image](https://user-images.githubusercontent.com/23014708/121547905-881ca700-ca47-11eb-8f6b-7bfe17a03ba3.png)



--
* [Mastering Issues](https://guides.github.com/features/issues/)
* [백엔드가 이정도는 해줘야 함 - 3. 개발 프로세스 정립](https://velog.io/@city7310/%EB%B0%B1%EC%97%94%EB%93%9C%EA%B0%80-%EC%9D%B4%EC%A0%95%EB%8F%84%EB%8A%94-%ED%95%B4%EC%A4%98%EC%95%BC-%ED%95%A8-3.-%EA%B0%9C%EB%B0%9C-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EC%A0%95%EB%A6%BD)
* [Understanding the GitHub flow](https://gist.github.com/9beach/dde37e22437642bb4f1f7f2afb208669)
* [Git flow, GitHub flow, GitLab flow](https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/)