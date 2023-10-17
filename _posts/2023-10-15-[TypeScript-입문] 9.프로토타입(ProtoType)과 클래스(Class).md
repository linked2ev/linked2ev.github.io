---
layout: post
title:  "[TypeScript-입문] 9.프로토타입(ProtoType)과 클래스(Class)"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

프로토타입(ProtoType)과 클래스(Class)에 대한 포스팅이다.

<br>

# 프로토타입(ProtoType)과 클래스(Class)

<br>

## Prototype 이란?

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain

자바스크립트에는 클래스라는 개념이 없는 대신 프로토타입(Prototype)이라는 것이 존재합니다.
그래서 자바스크립트가 프로토타입 기반 언어라고 불리며

자바스크립트에서는 클래스가 없으니 기본적으로 상속기능은 없으나 프로토타입을 기반으로 상속을 흉내내도록 구현해 사용한다.

```js
var user = {
  name: 'kim', age: 14
};

//admin 객체에서 user라는 객체를 prototype으로 받으면
//상속 개념으로 객체가 아닌 prototype을 통해서 user 객체를 사용할 수 있다.
var admin = {};
admin.__proto__ = user;
```

```
admin       //출략 : {}
admin.name  //출력 : kim
admin.age   //출력 : 14

//prototype으로 user 정보를 가지고 있는 형태
admin
  {role : 'admin'}
  __proto__:
    name: 'kim'
    age: 14
```

자바스크립트 Object, Array 내부 API 인 Object.assign(), Array.push() 등도 prototype으로 사용하는 것 있다.

- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array

<br>

## Class 와 Prototype

ES5에서 프로토타입으로 기반으로 생성자를 사용했으나  
ES6에서부터는 Class라는 키워드로 객체지향 형태로 생성자를 생성 할 수 있음.

```js
function Es5Person(name, age) {
  this.name = name;
  this.age = age;
}
var es5 = new Es5Person("세호", 30);
console.log(es5);


class Es6Person {
  private name: string;
  private age: number;
  //클래스 로직
  constructor(name, age) {
    console.log("생성자 생성");
    this.name = name;
    this.age = age;
  }
}
var es6 = new Es6Person("세호", 30);
console.log(es6);
```

<br>


## ES2015 (ES6) Class 문법

자바의 클래스 개념과 동일하다.

```js
class Person {
  name: string;
  private name1: string;
  public age: number;
  readonly log: string;

  //클래스 로직
  constructor(name: string, age: number) {
    console.log("생성자 생성");
    this.name = name;
    this.age = age;
  }

  public setName1(name1: string) {
    this.name1 = name1;
  }

  public getName1() {
    return this.name1;
  }
}

let pp = new Person("세호", 30);
console.log(pp);
```

<br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html
- https://opentutorials.org/course/743/6573