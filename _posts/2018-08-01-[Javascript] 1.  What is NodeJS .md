---
layout: post
title:  "[Javascript] 1. Node.js란?"
subtitle:   "[Javascript]"
categories: devlog
tags: Javascript
comments: true
---

우리가 알고 있는 Node.js란?  
<br>


[![nodejs step1](/assets/img/devlog/201808/2018-08-01-nodejs-step1.png)]() 

## 1. Node.js란?

>Node.js를 실제로 사용해보지 않은 사람들은 Node.js를 자바스크립트 백엔드 언어, 서버사이드 언어라는 것으로는 알고 있다. 하지만 그 외에 V8 Javascript엔진인 [런타임](http://asfirstalways.tistory.com/99) 환경에서의 기능에 대해서는 잘 모르는 것 같다. 다들 키워드랑 개념은 알지만 실제로 직접 안쓰면 잘 모른다.  
(필자도 마찬가지였다. 그리고 아직도 어렵다.)


자바스크립트가 Browser에서만 동작했지만, Node.js(런타임환경)에서도 자바스크립트가 동작된다고 생각하면 빠르다. 차이점을 예를 들어 Browser에서는 window지만 Node에서는 global이다.


하여튼, V8 자바스크립트 엔진을 웹 브라우저가 아닌 런타임환경으로 사용할 수 있도록 만든 것이 Node.js이다.


## 2. npm이란?

npm(Node Packaged Modules)은 `Node.js로 만들어진 모듈을 인터넷에서 받아서 설치해주는 패키지 매니저`이다.

이미 누군가가 만들어서 올려놓은 [모듈들을](https://www.npmjs.com/) 다운로드 받아서 쓰는 것이다.
<br>

>필자는 그래서 Node.js가 중요하다고 생각된다. 그리고 직접 써봐야 그나마 이해가 조금 된다.

<br><br>

---
참고
+ [Node.js 개념](http://pyrasis.com/nodejs/nodejs-HOWTO)
+ [Node.js 특징](http://asfirstalways.tistory.com/43)
