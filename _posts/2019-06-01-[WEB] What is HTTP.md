---
layout: post
title:  "[WEB] HTTP 이란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

HTTP 이란?

<br>


# 1. HTTP란?

`HyperText Transfer Protocol의 약자로 하이퍼텍스트 문서를 교환하기 위하여 사용된 통신 규약입니다.` 즉, 웹 서버와 클라이언트 간의 통신을 하기 위한 통신 규약입니다. HTTP는 1989년 팀 버너스-리에 의해 처음 설계되어 인터넷을 통한 월드 와이드 웹(World-Wide Web 일명 : www) 기반에서 전 세계적인 정보 공유를 이루는데 큰 역할을 했습니다. 

<br>


다시 정리하면, 인터넷은 TCP/IP 프로토콜 기반의 전세계적인 거대 네트워크이고 우리는 인터넷 서비스 중에 HTTP 프로토콜을 이용하는 WWW(World Wide Web)웹 서비스를 이용하는 것이다.

<br><br>


# 2. HTTP의 특징

- TCP/IP를 이용하는 응용 프로토콜(application protocol)
- HTTP 메시지는 HTTP 서버와 HTTP 클라이언트에 의해서 해석
- 비연결성(Connectionless)
    + 하나의 클라이언트의 request(요청)에 서버의 response(응답) 후 연결 끊김
- 무상태(Stateless)
    + 상태 정보를 유지하지 않는다
- 비연결성/무상태 단점을 해결하기 위해 Cookie와 Seesion 등장

<br><br>


# 3. HTTP 메시지

## 3-1. 메시지 타입

HTTP 메시지는 서버와 클라이언트 간에 데이터가 교환되는 방식입니다. 메시지 타입은 두 가지가 있습니다. 

- 요청(request)은 클라이언트가 서버로 전달하는 메시지
- 응답(response)은 요청에 대한 서버의 답변입니다.

<br>

## 3-2. 메시지 구조

Request/Response 메시지 전체적인 구조는 비슷하다.

- 시작 줄(start-line)
    + 실행되어야 할 요청, 또은 요청 수행에 대한 성공 또는 실패
    + 이 줄은 항상 한 줄로 끝남
- 두 번째 줄(http headers)
    + 요청과 응답 메세지에 대한 추가적인 정보를 담고 있다.
    + Key/Value 형식
    + 문서의 본문에 대한 정보 제공
    + ex) User-Agent, Accept, Server, Content-Length
- 빈 줄(blank line)
    + 요청에 대한 모든 메타 정보가 전송되었음을 알림
- 본문(body)
    + 실제 응답 리소스 데이터


[![web-http_1](/assets/img/devlog/201906/web-http_1.png)]()


<br>

# 4. HTTP 요청 메서드

서버가 수행해야 할 동작을 나타냅니다.

<br>

## GET (SELECT)
---

특정 리소스를 요청합니다. GET을 사용하는 요청은 오직 데이터를 받기만 합니다.

## HEAD
---

GET 메소드의 요청과 동일한 응답을 요구하지만, 헤더만 가져온다.

## POST (INSERT)
---

특정 리소스에 엔티티를 제출할 때 쓰입니다. 서버에 데이터를 전송해 처리를 요청한다. 

## PUT (UPDATE)
---

서버에서 저장 될 정보를 전달

## DELETE (DELETE)
---

특정 리소스를 삭제합니다.

## CONNECT
---

목적 리소스로 식별되는 서버로의 터널을 맺습니다.

## OPTIONS
---

지원되는 메소드 종류를 확인할 수 있다. 

## TRACE
---

목적 리소스의 경로를 따라 메시지 loop-back 테스트를 합니다.

## PATCH
---

리소스의 부분만을 수정하는 데 쓰입니다.




---
참고:  
[http://wiki.gurubee.net/pages/viewpage.action?pageId=26739929](http://wiki.gurubee.net/pages/viewpage.action?pageId=26739929)
[https://toma0912.tistory.com/69](https://toma0912.tistory.com/69)    
[https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP)  
[https://cascadingmedia.com/insites/2015/03/http-2.html](https://cascadingmedia.com/insites/2015/03/http-2.html)

