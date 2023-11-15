---
layout: post
title:  "[TypeScript-입문] 12.인터페이스(Interface) 추가 예제"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

오늘은 타입스크립트로 다시 와서~ 인터페이스(Interface)에 대한 포스팅이다.

<br>


# 1. 인터페이스(Interface) 추가 예제

# 1-1. optional property 예제

```js
// 인터페이스
interface User {
  name: string;
  age?: number; //age는 없을수도 있다는 것을 의미
  readonly: gender; //수정불가
  [index: string]: any; //indexable-type 어떤 이름의 property로 어떤 데이터 타입도 상관 없다는 의미
}

// 변수에 사용하는 경우
// gender 속성은 readonly로 수정불가
const p1: User = { 
  name: '준영', 
  age: 34,
  gender: 'M'
};

// any 예제
const p2: User = { 
  name: '으장' 
};

// indexable-type 예제
// job, lang property 에 각각 string과 array 데이터 타입
const p3: User = { 
  name: '진형',
  job: 'Dev', 
  lang: ['KOR', 'ENG', 'CHN']
};
```

<br>


# 2. 인터페이스 메서드 객체내 선언 예제

인터페이스에 메서드를 각 객체내에서 선언한 예졔이다.

```js
interface Lang {
  country: string;
  greetings(): void; 
}

const lang1: Lang = {
  country: 'KOREA',
  greetings(): void => {
    console.log("안녕하세요!")
  }
}

const lang2: Lang = {
  country: 'USA',
  greetings(): void => {
    console.log("Hello!")
  }
}

const lang3: Lang = {
  country: 'CHINA',
  greetings(): void => {
    console.log("NI HAO!")
  }
}
```

<br><br>


# 3. 인터페이스 메서드 클래스 구현 예제

```js
interface Lang {
  country: string;
  msg: string;
  greetings(): void; 
}

class LangA implements Lang {
  country: string;
  msg: string;

  constructor(country: string, msg: string) {
    this.country = country; 
    this.msg = msg; 
  }

  greetings(): void {
    console.log(`클래스 구현 : 국가 ${this.country} 인사는 ${this.msg}`)
  }
}
```

```js
const korea = new LangA("KOREA", "안녕하세요!");
korea.greetings();

const usa = new LangA("USA", "Hello!");
usa.greetings();

const china = new LangA("CHINA", "NI HAO!");
china.greetings();
```

```
클래스 구현 : KOREA 인사는 안녕하세요!
클래스 구현 : USA 인사는 Hello!
클래스 구현 : CHINA 인사는 NI HAO!
```

<br><br>


---