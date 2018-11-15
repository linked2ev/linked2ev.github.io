---
layout: post
title:  "[Javascript] 2. 서버 사이드 렌더링(SSR:Server Side Rendering)이란?"
subtitle:   "[Javascript]"
categories: devlog
tags: javascript
comments: true
---

서버 사이드 렌더링(SSR)에 대한 지식들과 개념을 정리해서 포스팅하자

<br>


## 1. 렌더링
---

- 렌더링은 요청 받은 내용을 브라우저 화면에 표시하는 것
1. 로더(Loader)가 서버로부터 정보들을 불러옴
2. 파싱(Phasing)을 통해 문서를 DOM 트리를 만듬
3. DOM 트리가 구축되는 동안 브라우저는 렌더 트리를 구축한다.
4. CSS 설정/레이아웃 위치 지정
5. 렌더링 트리가 그려짐

<br>


## 2. 서버 사이드 렌더링(SSR)
---

- SSR : Server Side Rendering
- 전통적인 웹 어플리케이션은 `서버 사이드 렌더링` 방식 [(참고-JSP처리과정)](https://linked2ev.github.io/devlog/2018/11/11/JSP-5.-JSP-Compile/)
- 요청시마다 서버에서 처리 한 후 새로고침으로 페이지에 대한 응답(View)
- 웹에서 많은 정보와 기능이 많아지면서 SPA개념이 생김

<br>


## 3. 클라이언트 사이드 렌더링(CSR)
---

- CSR : Client Side Rendering
- 클라이언트에서 자바스크립트를 통해 렌더링 하는 방식
- SPA : Single Page Application [(참고-SPA)](https://linked2ev.github.io/devlog/2018/08/01/WEB-1.-What-is-SPA/)
- SPA는 `클라이언트 사이드 렌더링` 방식

<br>

> 전통적인 페이지 vs 단일 페이지 어플리케이션 비교

[![SPA step1](/assets/img/devlog/201808/2018-08-01-SPA-step1.png)]() 

<br>

> ### 싱글 페이지 어플리케이션(SPA)
---

SPA는 브라우저에 로드되고 난 뒤에 페이지 전체를 서버에 요청하는 것이 아니라 
최초 한번 전체 페이지를 다 불러오고 HTML에 번들링 된 js가 실행되면서 페이지 렌더링을 한다.

그리고 렌더링 후 클라이언트 요청에 따라 응답 데이터만 다시 페이지의 특정 부분만 렌더링하는 웹 어플리케이션 개념이다.

<br>


## 4. 클라이언트 사이드 렌더링(CSR)와 서버 사이드 렌더링(SSR)
---

[![CSR&SSR-step1](/assets/img/devlog/201811/2018-11-15-CSR&SSR-step1.png)]() 

<br>


- 초기 View 로딩 속도
    - CSR은 최초 로딩 시 각종 HTML, JS, CSS 등 resource를 다 불러오고 js가 실행되면서 렌더링하기에 SSR보다는 맨 처음에는 View화면이 늦게 뜨는 현상이 있다.
    - CSR은 최초 로딩 이후에는 요청들에 대해서는 인터랙션이 빠르다.
    - SSR는 View를 서버에서 처리해서 초기로딩속도는 빠르지만 정보가 많은 B2C 웹 서비스 등에는 서버 부담이 크다.

<br>

- SEO(검색 엔진 최적화)의 문제
    - 일반적인 웹크롤러, 봇들은 HTML 내용만 수집하기에 빈 페이지로 인식한다. js가 실행이 완료되어 렌더링이 되기 전까지 HTML은 빈 화면이기 때문이다.

<br>

- 보안 문제
    - SSR에서는 사용자에 대한 정보를 서버 측에서 세션으로 관리를 했다.
    - CSR일 경우 쿠키말고는 사용자에 대한 정보를 저장할 공간이 마땅치 않다.

<br>

- 계속되는 진화
    - 위와 같은 문제를 해결하기 위해 구글은 계속  Anguler을 버전업이 되고, 페북은 아에 React는 서버사이드렌더링 문제를 고려하고 개발 되었다.   
    - React와 Vue로 만든 프론트 프레임워크인 Next.js와 Nuxt.js등이 나오고 지금도 github에 star를 받으면서 커밋이 되고 있다.

<br>


---
참고  
+ [d2.naver.com - 렌더링](https://d2.naver.com/helloworld/59361)
+ [CSR & SSR](http://asfirstalways.tistory.com/244)


