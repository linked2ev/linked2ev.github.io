---
layout: post
title:  "[WEB] HTTP 프록시(Proxy) 개념"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

프록시는 클라이언트의 요청을 받아 중계하는 서버이다.

<br>


## 1. Proxy 서버란?
---

Proxy 란 대리 혹은 중계 Agent 로써의 의미이며, 프록시 서버는 클라이언트의 요청을 받아 중계하는 서버이다.

## 클라이언트 <-> Proxy 서버 <-> 웹 서버

즉, 클라이언트가 요청를 보냈을 때 Proxy서버에서 웹 서버에 접근하여 요청과 응답을 처리한 후 Proxy서버에서 다시 클라이언트에게 응답을 한다.

<br>


## 2. Proxy 서버 특징
---

- 익명성으로 보안의 목적으로 사용
- 캐시를 이용한 요청 속도 개선
- 차단된 사이트를 우회하여 접속
- 원하지 않는 사이트를 차단

<br>


## 3. Proxy 서버 캐시 활용
---

웹 캐시(web cache) 또는 HTTP 캐시(HTTP cache)는 서버 지연을 줄이기 위해 웹 페이지, 이미지, 기타 유형의 웹 멀티미디어 등의 웹 문서들을 임시 저장한 후 

동일한 요청 시 프록시 서버의 웹 캐시에 저장된 정보를 불러오므로 트래픽이나 서버의 부하를 줄여 클라이언트가 원하는 정보를 빠르게  응답할 수 있다.

<br>


## 4. Proxy 서버 종류
---

프록시 서버가 서비스 서버에 작업하는 위치와 네트워크 구성에 따라서 크게 Forward Proxy/Reverse Proxy 로 구분된다.

<br>

> Forward Proxy (포워드 프록시)

일반적인 프록시 서버를 말하며, `클라이언트와 웹 서버의 중계역할`로 클라이언트가 요청 시 Proxy서버는 해당 요청을 웹 서버로 중계해 자원을 가져오는 개념이다.

프록시 서버는 클라이언트가 요청하기 전까지 웹 서버의 주소를 알 수 없다.

<br>


> Reverse Proxy (리버스 프록시)

`클라이언트와 내부망(Private Netowrk) 서버 사이에(앞에) 위치하여 제어역할`을 한다. 그래서 클라이언트가 요청을 하면 프록시 서버가 내부망 서버에 요청 후 응답 받은 자원을 클라이언트에게 전달해주는 개념이다.

리버스 프록시 서버는 실제 서버들에 대한 주소를 매핑하고 있어야 한다. 그리고 내부망에 서버에 대해 보안적으로나 로드밸런싱을 위해 사용되기도 한다.

<br><br>




---
참고:  
[https://ko.wikipedia.org/wiki/프록시_서버](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%EC%84%9C%EB%B2%84)  
[https://velog.io/@honeysuckle/HTTP-프락시Proxy의-개념](https://velog.io/@honeysuckle/HTTP-%ED%94%84%EB%9D%BD%EC%8B%9CProxy%EC%9D%98-%EA%B0%9C%EB%85%90)  
[https://dany-it.tistory.com/107](https://dany-it.tistory.com/107)  
[https://milkye.tistory.com/202](https://milkye.tistory.com/202)