---
layout: post
title:  "[TypeScript-입문] 2. 타입스크립트 기본 타입"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

타입스크립트에서 사용되는 기본 타입이다.

<br>

# 1. 타입스크립트 기본 타입

> 1_type-basic.ts
 
```js
// boolean
let isLogin: boolean = false;
console.log("> isLogin = ", isLogin);

// 문자열
const user: string = "Guest";
const msg: string = `Hello, ${user}`;
console.log("> string = ", msg);

// 숫자
const num: number = 10;
console.log("> num = ", num);

// 배열
const numArr: Array<number> = [1, 2, 3];
const strArr: Array<string> = ["one", "two", "three"];
//arr.push('hi');
console.log("> numArr = ", numArr, "\n> strArr = ", strArr);

// 배열 - 리터럴 적용
const numItems: number[] = [];
numItems.push(10);
numItems.push(20);
const strItems: string[] = [];
strItems.push("A");
strItems.push("B");
console.log("> numItems = ", numItems, "\n> strItems = ", strItems);

// 튜플
let employee: [string, number] = ["김준영", 89];
let employees: [string, number][] = [];
employees.push(["송해양", 87]);
employees.push(["임정수", 92]);
console.log("> employee = ", employee, "\n> employees = ", employees);

// 객체
const obj: object = {};
// obj.a = 10;

// 타입 객체
const person: { age: number; name: string } = { age: 100, name: "Capt" };
person.age = 101;
console.log("> person = ", person);

// any : 어떤 type도 가능함을 추론
let data: any = {};
data = false;
data = 12;
data = "12";

function retMsg(code: any): string {
  return "인자 어떤 type도 가능";
}

// void : 반환 값이 없는 함수의 반환 타입
function printLog(): void {
  console.log("sth");
}

// never : 함수의 끝에 절대 도달하지 않는다는 의미를 지닌 타입
function neverEnd(msg: string): never {
  //아래 로직들은 해당 메서드가 끝까지 가지 않는 로직
  throw new Error(msg); //or while (true) {}
}

// 이넘(Enum)
enum EngClass {
  Introductory,
  Beginner,
  Intermediate = 10,
  Advanced = 11,
}
console.log("> enum1 =", EngClass.Beginner);
console.log("> enum2 =", EngClass.Advanced);
```

```
$ npx tsc             
$ node 1_type-basic.js
> isLogin =  false
> string =  Hello, Guest
> num =  10
> numArr =  [ 1, 2, 3 ] 
> strArr =  [ 'one', 'two', 'three' ]
> numItems =  [ 10, 20 ] 
> strItems =  [ 'A', 'B' ]
> employee =  [ '김준영', 89 ] 
> employees =  [ [ '송해양', 87 ], [ '임정수', 92 ] ]
> person =  { age: 101, name: 'Capt' }
> enum1 = 1
> enum2 = 11
```


<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html