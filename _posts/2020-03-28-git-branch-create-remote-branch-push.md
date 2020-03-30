---
layout: post
title:  "Git Remote branch"
date:   2020-03-28
excerpt: "Git Loacal에서 Branch 만들고 Remote로 Push 하기"
categories: 
 - Git
tag:
 - git
 - branch
---

## Local에서 branch 생성하기
* `git branch [branchname]`: branchname으로 새로운 branch를 생성함
    * `newbrance`라는 이름으로 생성해보자.
* `git branch`: 현재 저장소의 branch 목록을 확인할 수 있음.

```linux
> git branch newbranch
> git branch
* master
  newbranch
```

## Local branch를 Remote branch로 올리기
* `git push [원격 저장소 이름] [로컬 브랜치 이름]`: 원격 저장소로 로컬 브랜치를 push 함.
    * 원격 저장소 이름은 보통 `origin`으로 명칭되어 있음.

```linux
> git push origin newbranch

...
To https://원격저장소주소
  * [new branch]    newbranch  ->  newbranch
```

## Remote branch 확인
* `git branch -r`: 원격 저장소의 브랜치 목록을 확인할 수 있음.

```linux
> git branch -r
  origin/HEAD -> origin/master
  origin/newbranch
  origin/master
```

---
* https://blog.outsider.ne.kr/644
* https://ychae-leah.tistory.com/59