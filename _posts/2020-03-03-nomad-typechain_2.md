---
layout: post
title:  "Typechain #5"
date:   2020-03-03
excerpt: "Academy|Nomad Coder Typechain 강의 #5"
categories: 
 -Course
tag:
 - typescript
 - blockchain
---

### 실습환경

|OS|Typescript|TSC-watch|
|:--|:--|:--|
|Windows 10|3.8.2|4.1.0|

## Interfaces 의 필요성

*  object를 넘기려면 어떻게 해야할까?
    * typescript가 object를 이해할 수 있게 해야 하고
    * object가 올바른 type인지 분별하게 해야 함.
* 아래와 같은 코드는 오류를 일으킬 것이다. `sayHi`는 3개의 변수값이 들어오길 준비하고 있기 때문에
* 이와 같은 상황에 `interface`가 필요하다.

```typescript
const person = {
    name: "hyeonsook",
    gender: "female",
    age: 26
}

...

console.log(sayHi(person));

export { };
```

## Interface 생성

* `interface [interface_name] { [args..]}` : interface를 선언하고 사용할 object와 연결해서 사용할 수 있다.
    * `[object_name].[arg_name]`: interface에 연결된 object의 속성을 사용할 때는 dot(.)을 통해 사용할 수 있다.
* `ingerface`는 js에서는 작동하지 않는다. typescript에서만 작동한다.

```typescript
nterface Human {
    name: string;
    age: number;
    gender: string;
}

...

const sayHi = (person: Human): string => {
    return `Hello ${person.name}, ${person.age}, ${person.gender}`;
}

console.log(sayHi(person));

export { };
```

---
* https://academy.nomadcoders.co/courses/303219/lectures/4975935