---
layout: post
title:  "[TypeScript-입문] 1. 타입스크립트부터 맛보기"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

타입스크립트로 시작해서 리액트까지 회독하면서 포스팅 정리 시작

<br>


# 1. TypeScript 시작

- https://www.typescriptlang.org/
- https://yamoo9.gitbook.io/typescript/
- https://joshua1988.github.io/webpack-guide/

typescript 는 자바의 제네릭처럼 자바스크립트에 타입을 정의하는 것으로 
자바의 제네릭 개념을 알면 동일해서 슥보고 사용기는 좋다.

<br>

## 1-1. npm 설치

root 경로에서 npm 설치

```
$ npm init -y
```

## 1-2. typescript 설치

```
$ npm i typescript -g
//typescript 기본 기능 플러그인 설처
$ npx tsc --init
```

<br>

# 2. typescript 맛보기

`tsc` 명령어로 파일을 컴파일을 하지만 실무에서는 Webpack을 이용해서 컴파일한다. Webpack은 모듈번들러이며 우선 밑에서는 우리가 흔히 보는 ts 파일의 뭔지 알아보자.

- TypeScript 컴파일을 할때 부가적인 옵션설정을 위한 파일이 `tsconfig.json` 파일
- [타입스크립트 설정 옵션](https://www.typescriptlang.org/docs/handbook/compiler-options.html)

<br>

> index.ts

```ts
function add(a: number, b: number): number {
    return a + b;
}

var result = add(10, 20);
console.log("> result = ", result);
```

`index.ts` 파일을 생성한 다음 "`$ npx tsc`" 터미널 명령어를 해당 디렉터리 기준으로  
ts 파일들이 컴파일 되면서 `index.js` 파일이 생성된다. 

그 다음에 node 명령어로 index.js 파일을 실행시켜보자

<br>

> index.js

```js
"use strict";
function add(a, b) {
    return a + b;
}
var result = add(10, 20);
console.log("> result = ", result);
```

```
$ node index.js 
> result =  30
```

<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html
- TypeScript Playground : https://www.typescriptlang.org/play?#code/FBA
- Babel Playground : https://babeljs.io/repl#?browsers