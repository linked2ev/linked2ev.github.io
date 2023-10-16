---
layout: post
title:  "[TypeScript-입문] 5.타입 가드(Type Guards)"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

타입 가드(Type Guards)에 관한 포스팅이다.

<br>


# 타입 가드(Type Guards)

타입 가드는 에러를 줄일수 있는 방어 코드 기법을 의미한다. 타입가드를 프로젝트시에 활용하면  
개발자들간에 오류를 최소화 할 수 있다.


### 타입 가드에서 사용되는 키워드

- typeof : 일반 타입 체킹
- instanceof : 클래스 체킹
- Array.isArray() : 배열 체킹
- type in 속성명 : 객체 속성 체킹

<br>

## 일반 타입 가드

```js
function typeGuard1(a: number | string) {
  if (typeof a === "string") {
    console.log("string...");
  }
  if (typeof a === "number") {
    console.log("number...");
  }
}
```

<br>

## 클래스 타입 가드

```js
class Fruit {
  order() {
    console.log("Fruit order...");
  }
}

function typeGuard2(inst: Fruit | string) {
  if (inst instanceof Fruit) {
    inst.order();
  } else {
    console.log(inst);
  }
}

typeGuard2(new Fruit());
typeGuard2("BOX");
```

<br>

## 객체 타입 가드

```js
type Korea = { type: "asia"; code: 1; actionA: "A" };
type USA = { type: "america"; code: 2; actionB: "B" };
type France = { type: "europe"; code: 3; actionC: "C" };

function typeCheck(country: Korea | USA | France) {
  if (country.type === "asia") {
    //...
  } else if (country.type === "america") {
    //...
  } else {
    //...
  }
}
```

<br>

## 타입 가드 커스텀 방식

```js
interface Lecturer {
  teach: string;
}
interface Student {
  learn: string;
}
// Student 타입인지 확인하는 타입가드 (리턴 타입에 is 키워드를 붙여야 기능함)
function catOrDog(person: Lecturer | Student): person is Student {
  // 타입 판별은 여러분이 직접 로직을 구현
  if ((person as Student).learn) {
    return true; // 학생이면  true
  } else {
    return false; // 강사면 false
  }
}
```

<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html