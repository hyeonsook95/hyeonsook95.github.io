---
layout: post
title:  "Basic Algorithms 02"
date:   2020-04-06
excerpt: "자료구조(스택)"
categories: 
 - Basic
tag:
 - algorithms
 - datastructs
 - stack
---

## 스택(Stack)
* 한쪽 끝에서만 자료를 넣고 뺄 수 있는 구조.
* 마지막으로 넣은 것이 가장 먼저 나옴(LIFO).
* 스택의 기능
    * push: 스택에 자료를 넣는 연산.
    * pop: 스택에 자료를 빼는 연산.
    * top: 스택의 가장 위에 있는 자료를 보는 연산.
    * empty: 스택이 비어있는지 아닌지를 알아보는 연산.
    * size: 스택에 저장되어 있는 자료의 개수를 알아보는 연산.

### 다른 언어들에서의 스택 사용

* c++ 에서 `stack` 사용.
```c++
#include <stack>
using namespace std;

int main(){

    int tmp = 10;
    stack<int> s;

    s.push(tmp);
    s.pop();

    s.top();
    s.empty();
    s.size();
}
```

* python 에서 `stack` 사용.
* python 에서는 `list`가 스택과 같은 기능을 함.
    * c++에서 제공하는 기능 중 python에서 제공하지 않는 기능들이 있었음. 정확한 명칭이 stack이 아닌 list니까
    * stack 문제를 풀 때 size나 empty, top은 자주 사용하는 기능이므로 같은 기능을 할 수 있는 방법을 적어놓음.

```python3
>>> stack = [3, 4, 5]
>>> stack.append(6)
>>> stack
[3, 4, 5, 6]
>>> stack.pop()
6
>>> stack
[3, 4, 5]

# 없는 기능들
>>> def is_empty(s):
        if not s:
            False
        else:
            True

>>> stack[-1]
5
>>> is_empty(s)
True
>>> len(stack)
3
```

### 문제에서 보이는 스택의 특징
* LIFO
* 문제를 해결하는 방법에서 마지막으로 나오는 데이터의 값이 의미가 있는 연산이 반복됨.

## 큐(Queue)
* 한쪽 끝에서만 자료를 넣고 다른 한쪽 끝에서만 뺄 수 있는 자료구조.
* 먼저 넣은 것이 가장 먼저 나옴(FIFO).
* 스택의 기능
    * push: 큐에 자료를 넣는 연산.
    * pop: 큐에 자료를 빼는 연산.
    * front: 큐의 가장 앞에 있는 자료를 보는 연산.
    * back: 큐의 가장 뒤에 있는 자료를 보는 연산.
    * empty: 큐가 비었는지 아닌지를 알아보는 연산.
    * size: 큐에 저장되어 있는 자료의 개수를 알아보는 연산.