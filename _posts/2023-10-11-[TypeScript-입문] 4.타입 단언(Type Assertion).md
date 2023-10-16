---
layout: post
title:  "[TypeScript-입문] 4. 타입 단언(Type Assertion)"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

타입 단언(Type Assertion)에 관한 포스팅이다.

<br>


# 타입 단언(Type Assertion)

타입 단언은 개발자가 해당 타입에 대해 확신이 있을 때 사용하는 타입 지정 방식입니다. 다른 언어의 타입 캐스팅과 비슷한 개념이며 타입스크립트를 컴파일 할 때 특별히 타입을 체크하지 않고, 데이터의 구조도 신경쓰지 않습니다. 개발자가 타입스크립트에게 해당 타입은 내가 지정하겠다는 의미로 볼 수 있다.

<br>

# 타입 단언 기본 - as

타입 단언은 기본적으로 as 키워드를 이용해서 정의할 수 있습니다. 아래와 같은 코드가 있다고 합시다.

```js
const name: string = 'Capt';
```

이 코드는 타입 표기 방식을 이용해 name 이라는 변수의 타입은 string 이라고 정의한 코드입니다. 이 코드에 타입 단언을 적용하면 다음과 같습니다.

```js
const name = 'Capt' as string;
```

비주얼 스튜디오 코드에서 name 변수의 정보를 확인해 보면 동일하게 string으로 추론되는 것을 확인할 수 있습니다.

<br>

## 타입 단언 예제

```js
interface Hero {
  name: string;
  age: number;
}

//타입 단언에 의해 name, age 등의 속성이 없다고 오류가 발생함
const student: Hero = {};
student.name = "홍길동";
student.age = 34;

//1. 객체 내에 속성을 정의하는 방법
const student2: Hero = {
  name: "홍길동",
  age: 34,
};

//2. as 키워드로 타입 문제를 해결
const student3 = {} as Hero;
student.name = "홍길동";
student.age = 34;

```


<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html