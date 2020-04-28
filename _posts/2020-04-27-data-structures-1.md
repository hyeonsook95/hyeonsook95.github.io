---
layout: post
title:  "데이터 구조(1)"
date:   2020-04-27
excerpt: "데이터 구조"
categories: 
 - Data Structures
tag:
 - datastructure
 - linkedlist
---

## Linked List
* Node: Data와 다른 노드를 가리키는 연결(Link)로 이루어진 Linked List의 단위
* Linked List는 Node간의 연결을 이용하여 리스트를 구현한 것
* Linked List에서 중요한 것은 연결이 무엇인가를 파악하는 것

### Linked List의 장점
* Linked List의 길이를 동적으로 조절할 수 있음.
* 데이터의 삽입과 삭제가 쉬움.

### Linked List의 단점
* 임의의 노드에 바로 접근할 수 없음.
* 다음 노드의 위치를 저장하기 위한 추가 공간이 필요함.
* Cache Locality를 활용해 근접 데이터를 사전에 캐시에 저장하기가 어려움.
* 거꾸로 탐색하기가 어려움.

### Node 클래스 구현
* Data
    * data: 노드의 내용을 담을 데이터
    * next: 다음 노드를 가리킬 데이터

### Linked List 클래스 구현
* Data
    * head: 맨 앞
    * before: 현재 탐색 위치 전
    * current: 현재 탐색 위치
    * tail: 맨 뒤
    * num_of_data: 데이터의 총 개수
* Method
    * 생성자: Data 초기화
    * 생성
        * 노드가 없을 때 추가
        * 노드 중간에 추가
        * 마지막 노드 뒤에 추가
    * 삭제
        * 노드가 없을 때 삭제
        * 중간 노드 삭제
        * 마지막 노드 삭제
    * 검색
        * .first(): 맨 앞의 노드 검색(search)
        * .next(): 다음 노드 검색(search)


---
* [[해외명강] 컴퓨터 과학 교양 강좌: CS50](https://www.edwith.org/cs50/lecture/22799/)