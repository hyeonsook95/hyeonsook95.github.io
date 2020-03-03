---
layout: post
title:  "Typechain #3 #4"
date:   2020-02-22
excerpt: "Academy|Nomad Coder Typechain 강의 #3 #4"
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

> 3강부터 듣고 있는데 앞의 두 개 강의 메모 안해서 다시 들어야 함.

# Typescript

* Typed Language 임.
   * 어떤 종류의 변수와 데이터인지 설정을 해줘야 함.
   * return 이 어떤 종류인지, 함수에서 사용하는 변수의 종류는 어떤 것인지 등.

   ```typescript
   const name="hyeonsook",
    age = 26,
    gender = "female;
   ```

## export {};  

위 코드에서 이름을 선언할 수 없다고 뜬다면, 이 파일이 모듈인걸 알리기 위해 export{};를 선언해주면 됨.

```typescript
...

export {};
```

## function parameter 을 optional 하도록

해당 파라미터 값을 받을 수도 받지 않을 수도 있도록 해줌.
   * `[parameter_name]?`

```typescript
const name="hyeonsook",
 age = 26,
 gender = "female;

const hello = (name, age, gender?) => {
    ...
}

hello(name, age);
```

## Function's data type

### Parameters type

파라미터를 설정하는 방법은 2가지가 있음.

첫 번째로 파라미터로 사용할 변수를 먼저 선언해주는 방법.

```typescript
const name="hyeonsook",
  age = 26,
  gender = "female;

const hello = (name, age, gender) => {
    return `Hello ${name}, ${age}, ${gender}`;
}
```

두 번째는 함수를 선언할 때 변수의 타입을 설정하는 방법.  
   * `[parameter_name]: [datatype]`

```typescript
const hello = (name: string, age: number, gender: string) => {
    return `Hello ${name}, ${age}, ${gender}`;
}
```

### Return type

   * `const [func_name]([parameters]): [datatype]`

```typescript
const hello = (name: string, age: number, gender: string): string => {
    return `Hello ${name}, ${age}, ${gender}`;
}
```

## npm start

typescript를 compile하는 과정을 자동화하자.

### TSC watch 패키지 설치.

```linux
> npm add tsc-watch --only=dev

```

### package.json 파일 변경.

  * --onSuccess: onSuccess 이면 command(`node index`) 실행.
  * Error: Cannot find module 'typescript/bin/tsc'  
    * --compiler option을 넣어줌.
    * `--compiler [global하게 설치된 tsc 파일의 위치]`

```json
"scripts": {
    "start": "tsc-watch --onSuccess \" node index.js  --compiler [USERPROFILE]\\AppData\\Roaming\\npm\\tsc"
},

```

### src, dist 폴더 생성.

   * .js 파일을 dist 폴더로 이동시킴.

### tsconfig.json 파일 변경.

   * `"include":["src/**/*"],`: src 폴더 안의 파일(.ts)들을 컴파일
   * `"compilerOptions": {"outDir": "dist",},`: src를 컴파일한 것들을 dist에 저장.

```json
"compilerOptions": {
    "module": "commonjs",
    "target": "ES2015",
    "sourceMap": true,
    "outDir": "dist",
},
"include": [
    "src/**/*"
],

```

### npm start

자동화 과정이 끝났음. 이제 src가 변경될때마다 dist가 변하는 모습을 매번 `npm start`하지 않고도 볼 수 있음.