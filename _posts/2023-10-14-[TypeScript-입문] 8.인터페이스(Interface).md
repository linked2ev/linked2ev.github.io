---
layout: post
title:  "[TypeScript-입문] 8.인터페이스(Interface)"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

인터페이스(Interface)에 대한 포스팅이다.

<br>


# 인터페이스(Interface)

인터페이스는 상호 간에 정의한 약속 혹은 규칙을 의미합니다. 타입스크립트에서의 인터페이스는 아래와 같은 특성으로 사용된다.

- 객체의 스펙(속성과 속성의 타입)
- 함수의 파라미터
- 함수의 스펙(파라미터, 반환 타입 등)
- 배열과 객체를 접근하는 방식
- 클래스

<br>


## 인터페이스 예제

```js
// 인터페이스
interface User {
  name: string;
  age: number;
}

// 변수에 사용하는 경우
const bro: User = { name: '으장', age: 37 };

// 함수의 매개변수에 사용하는 경우
function getUser(user: User) {
  console.log(user);
}
getUser(bro);
```

<br>

## 인터페이스 확장 예제

```js
interface Person {
  name: string;
  age: number; // 옵셔널 선택자 ? 동일하게 적용 가능
}
interface Developer extends Person {
  language: string;
}
const joo: Developer = { name: "joo", age: 20, language: "ts" };
```

<br>

## 인터페이스 구현 예제

```js
interface Person {
  name: string;
  say(message: string): void;
}

interface Programmer {
  coding(requirement: string): string;
}

class Employee implements Person, Programmer {
  constructor(public name: string) {}

  //구현
  say(message: string): void {
    console.log("# message=", message);
  }

  //구현
  coding(requirement: string): string {
    console.log("# requirement=", requirement);
    return "요구사항" + requirement;
  }
}
```

<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html
- https://joshua1988.github.io/vue-camp/es6+/modules.html#모듈화의-필요성
- https://d2.naver.com/helloworld/12864