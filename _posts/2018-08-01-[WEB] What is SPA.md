---
layout: post
title:  "[WEB] SPA 란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

SPA 중심에는 대표적인 라이브러리/프론트프레임워크 React, Vue/Angular가 있다.


## 1. SPA란?

SPA란 Single Page Application의 약자이다.

단일 페이지 어플리케이션(SPA)는 현재 웹개발의 트랜드이다.

기존 웹 서비스는 요청시마다 서버로부터 리소스들과 데이터를 해석하고 화면에 렌더링하는 방식이다.
SPA형태는 브라우저에 최초에 한번 페이지 전체를 로드하고, 이후부터는 특정 부분만 Ajax를 통해 데이터를 바인딩하는 방식이다. 
<br><br>

> 전통적인 페이지 vs 단일 페이지 어플리케이션 비교

[![SPA step1](/assets/img/devlog/201808/2018-08-01-SPA-step1.png)]() 


예전부터 개발자들은 지속적으로 웹서비스와 개발방식을 발전시켜왔다. CSS, JS 리소스 등을 cdn형태로 캐싱 및 압축하고, View에서 
[템플릿엔진](https://www.slideshare.net/SeongSikChoi/java-script-template-engine) 들을 사용하고, 
초기의 SPA 개념인 Backbone.js, Angular.js 라이브러리들이 나왔고, 지금은 템플릿 개념을 지나 컴포넌트 개념인 React.js, Vue.js, Angular2 등 이렇게 다양한 라이브러리와 프레임워크가 등장했고 아직도 자바스크립트는 발전해가고 있다.  
<br><br>

> 컴포넌트 개념

[![SPA step2](/assets/img/devlog/201808/2018-08-01-SPA-step2.png)]()

위의 이미지 처럼 컴포넌트들이 모여 한 페이지를 작성하고, 특정 부분만 데이터를 바인딩하는 개념이다.
<br><br>


>SPA 구현을 쉽게 말하면 jsp파일 없이 index.html파일 하나에서 js, css등 리소스 파일들과 모듈들을 로드해서 페이지 이동 없이 특정영역만 새로
모듈을 호출하고 데이터를 바인딩하는 개념이다.  
<br>
물론 이와 같이 개발하기 위해서는 위해서는 ES6, Node.js와 npm 그리고 webpack, Babel 같은 번들러 등 개념을 한번 정도는 잡고 접근해야 할게 많다.

<br>

> SPA 중심에 Vue, Angular, React

[![SPA step3](/assets/img/devlog/201808/2018-08-01-SPA-step3.png)]()

지속적인 발전으로 대표적인 라이브러리/프론트프레임워크 React, Vue/Angular가 있다.
- [Javascript는 지속적으로 발전하고 있다.](https://d2.naver.com/helloworld/3259111)
- [React, Vue, Angular는 환경과 목적에 맞게 사용해야 한다.](https://www.slideshare.net/GunheeLee2/angular-react-vue)
- [React, Vue, Angular 간단한 비교](http://blog.jeonghwan.net/vue/2017/03/29/is-vue-better-than-angular-react.html)  
<br><br>


한 페이지에서 대충 적기만 했는데도 정말 알아야 할게 많다.  
그면 천천히 알아가보자~


---
참고
- [SPA란 무엇인가?](http://devstory.ibksplatform.com/2017/08/spasigle-page-applications.html)
- [SPA의 단점](http://m.mkexdev.net/374)
- [SPA 라우팅 원리](http://reimaginer.tistory.com/entry/spa-and-spa-routing)
