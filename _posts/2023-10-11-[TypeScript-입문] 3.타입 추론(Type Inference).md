---
layout: post
title:  "[TypeScript-입문] 3. 타입 추론(Type Inference)"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

타입 추론(Type Inference)에 대한 포스팅이다.

<br>


# 타입 추론(Type Inference)

타입 추론이란 타입스크립트가 코드를 해석해 나가는 동작을 의미합니다.

<br>

# 타입 추론의 기본

타입스크립트가 타입 추론을 해나가는 과정은 다음과 같습니다.

```js
let x = 3;
```

위와 같이 x에 대한 타입을 따로 지정하지 않더라도 일단 x는 number로 간주됩니다. 이렇게 변수를 선언하거나 초기화 할 때 타입이 추론됩니다. 이외에도 변수, 속성, 인자의 기본 값, 함수의 반환 값 등을 설정할 때 타입 추론이 일어납니다.

그래서 아래와 같이 타입스크립트에서는 아래와 같이 코딩이 되어 있으면  
타입추론에 의해 number 타입에 string 타입을 할당할려고 해서 에러가 발생한다.


```js
let x = 3;
x = 'Hello!'; // Error - TS2322: Type '"Hello!"' is not assignable to type 'number'.
```

<br>

### nolmplicitAny 옵션

파라미터 타입을 명시적으로 지정하지 않는 경우, 타입스크립트가 추론 중 `any`라고 판단되면  
컴파일 에러를 발생시켜 명시적으로 지정하도록 유도한다.

<br>


## 함수를 이용한 예제

```js
// 타입스크립트 함수 선언 - 함수의 매개 변수
function add(a: number, b: number) {
  return a + b;
}

// 타입스크립트 함수 선언 - 함수의 반환 타입
function add(a: number, b: number): number {
  return a + b;
}

// 함수 파라미터 제한
function log(a: string) {
  console.log(a);
}
log("a");

// 함수의 옵션널 파라미터
function log2(a: string, b?: number) {
  console.log(a, b);
}
log2("a");
log2("b", 10);

```

<br>

## 인터페이스를 통한 타입 추론 예제

```js
interface Item<T> {
  value: T//<T> : number
  title: string;
}
var items: Item<number> = {
  value: 10,
  title: 'a'
}
```

<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html