---
layout: post
title: "SQL Optimizer"
date: 2020-02-07
excerpt: "SQL에서 Optimizer이란?"
categories:
  - SQL
tag:
  - sql
  - db
  - optimizer
---

##  SQL Optimizer 이란?

SQL Optimizer가 무엇인지 직관적으로 받아들이기 위해 단어를 분리해 알아보자.

### SQL 이란?

SQL은 관계형 데이터베이스 관리 시스템(RDBMS)의 데이터를 관리하기 위해 설계된 특수 목적의 프로그래밍 언어를 말한다. 쉽게 풀이하자면 RDBMS에게 데이터에 관한 생성, 삭제, 조회 등의 조작들을 요청하는 말이라는 뜻이다.

### Optimizer 란?

Wiki에 Optimizer를 검색하면 다양한 Optimization들이 나온다. Optimization들을 보다보면 이 Optimizer들은 사용 분야에서 최적의 상태를 위해 발버둥치는 것들을 의미한다고 짐작할 수 있다.

### 그렇다면 SQL Optimizer은?

**RDBMS에 질의된 SQL을 최적화 해주는 것** 이라고 생각할 수 있고, 말 그대로 SQL Optimizer은 SQL을 가장 빠르고 효율적으로 수행할 최적(가장 낮은 비용=최저비용)의 실행 방법을 생성하고 결정하는 DBMS 내부의 핵심엔진을 말한다.

옵티마이저는 사용자가 SQL로 데이터를 요구하면 결과를 응답하기 위해 처리경로를 생성한다. 이 때 옵티마이저가 생성한 SQL 처리경로를 실행계획(Execution Plan)이라고 부른다.

## Optimizer의 종류

옵티마이저는 최적의 처리경로를 결정하는 방식에 따라 두 가지로 나뉜다. (현재 대부분의 RDBMS는 비용기반 옵티마이저만을 제공하고 규칙기반 옵티마이저를 제공하더라도 하위 버전 호환성을 위해서만 남아있다.)

1. 규칙기반 옵티마이저(Rule-Based Optimizer, RBO)  
   
   휴리스틱 옵티마이저(Heuristic Optimizer)라고도 하며, 정해 놓은 규칙(우선 순위)를 가지고 실행계획을 선택한다. 
   
   >우선순위를 결정하는 요인
   >* SQL문을 실행하기 위해서 이용 가능한 인덱스 유무(유일, 비유일, 단일, 복합 인덱스)와 종류
   >* SQL문에서 사용하는 연산자(=, <, <>, LIKE, BETWEEN, 등)의 종류
   >* SQL에서 참조하는 객체(힙 테이블, 클러스터 테이블 등)의 종류

2. 비용기반 옵티마이저(Cost Based Optimizer, CBO)  
  
   옵티

---
* http://www.gurubee.net/article/10134
