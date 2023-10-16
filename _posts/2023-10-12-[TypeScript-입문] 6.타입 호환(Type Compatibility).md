---
layout: post
title:  "[TypeScript-입문] 6.타입 호환(Type Compatibility)"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

타입 호환(Type Compatibility)에 관한 포스팅이다.

<br>


# 타입 호환(Type Compatibility)

타입 호환이란 단어 그대로 타입스크립트 코드에서 특정 타입 간에 구조가 비슷하면 타입 호환이 될 수 있다는 것을 의미한다.

<br>


## 타입 호환

```js
interface Person {
  name: string;
  age: number;
}
class Lecturer {
  name: string = "";
  age: number = 0;
  teach: string = "";
}
class Student {
  name: string = "";
  age: number = 0;
  learn: string = "";
}
```

<br>

## 제네릭 타입 호환

```js
interface Empty<T> {}

let x: Empty<number>;
let y: Empty<string>;
x = y;

//오른쪽 타입이 속성이 더 큰 개념으로 합집합이어야 가능함
let p1: Person;
let p2: Person;
let lecturer: Lecturer;
let student: Student;
```

<br>

## 인터페이스 타입 호환

```js
interface Player {
  run: () => void;
}
interface Player {
  shoot: () => void;
  pass: () => void;
}

const p: Player = {
  run(): void {
    console.log("runnn");
  },
  shoot(): void {
    console.log("shoot!");
  },
  pass(): void {
    console.log("pass~");
  },
};
```

<br>

## 클래스 타입 호환

```js
class Bus {
  capacity: number;

  constructor(capacity: number) {
    this.capacity = capacity;
  }
}
class Subway {
  capacity: number;
  subwayRoute: number;

  constructor(capacity: number, subwayRoute: number) {
    this.capacity = capacity;
    this.subwayRoute = subwayRoute;
  }
}

//에러 발생 : Subway 가 속성이 더 큰 합집합 개념으로 에러 발생
//let t1: Subway = new Bus(40);
//console.log(t1.capacity);

let t2: Bus = new Subway(150, 2);
console.log(t2.capacity);
```

<br>

## Union, Intersection 타입 호환

```js
interface TV {
  name: String
  price: Number
}

interface Manufacturer {
  name : String
  country: String
}

//Union Type ('|')을 통해 교집합 속성
function setUnionTv(item: TV | Manufacturer) {
  //TV, Manufacturer 인터페이스의 공통속성인 name만 지원하고 있다.
  item.name
  //item.price//error
  //item.country//error
}

//Intersection Type ('&')을 통합 합집합 속성
function setIntersectionTv(item: TV & Manufacturer) {
  //TV, Manufacturer 인터페이스의 공통속성인 name만 지원하고 있다.
  item.name
  item.price
  item.country
}

setIntersectionTv({
  name: '삼성GLED-TV',
  price: 1000000000,
  country: 'KOREA'
})
```

<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html