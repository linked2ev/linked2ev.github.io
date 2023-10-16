---
layout: post
title:  "[TypeScript-입문] 7.모듈화(Modularization)"
subtitle: "[TypeScript]"
categories: typescript
tags: typescript-introductory
comments: true
---

모듈화(Modularization)에 대한 포스팅이다.

<br>


# 모듈화(Modularization)

임포트(Import)와 익스포트(Export)는 자바스크립트의 코드를 모듈화 할 수 있는 기능입니다.  
여기서 모듈화란 쉽게 말해서 다른 파일에 있는 자바스크립트의 기능을 특정 파일에서 사용할 수 있는 것을 의미합니다.

모듈화의 필요성은 프로젝트 개발시에 많이 겪듯이 js파일들의 변수명 및 함수명 등이 범위문제로 관리가 안되는 경우가 있다.
ES6에서 지원하는 import, export로 웹팩(Webpack) 등 같은 모듈 번들러를 이용한다.

<br>

## Import & Export 예제

> types.ts

```js
export interface Todo {
    title: string;
    checked: boolean;
}
```

> app

```js
import { Todo } from './types'

var item: Todo = {
    title: '할일-1',
    checked: false
}
```

<br><br>


---
[참고]
- 타입스크립트 핸드북 : https://joshua1988.github.io/ts/intro.html
- https://joshua1988.github.io/vue-camp/es6+/modules.html#모듈화의-필요성
- https://d2.naver.com/helloworld/12864