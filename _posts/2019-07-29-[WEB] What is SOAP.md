---
layout: post
title:  "[WEB] SOAP 란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

SOAP란?


<br>


# SOAP(Simple Oject Access Protocol)란?

HTTP, HTTPS, SMTP 등을 통해 XML 기반의 메시지를 컴퓨터 네트워크 상에서 `메시지를 교환하기 위한 통신규약 프로토콜`이다. 

그래서 데이터들은 SOAP 방식으로 전달 된다.

<br>

SOAP은 디자인 패턴으로 설계되어 있다. '헤더'는 선택사항으로 반복이나 보안 및 트랜잭션을 정보로 하는 메타 정보를 가지고 있다. '바디'부분은 주요한 정보인 정보를 가지고 있다.

[![soap-s1](/assets/img/devlog/201907/soap-s1.png)]()

<br>

# SOAP 특징

- XML형식 메시지 처리
- 프록시와 방화벽에 구애받지 않고 쉽게 통신 가능
- 플랫폼 및 프로그래밍 언어에 독립적
- 멀티파트 MIME 구조

<br>

# SOAP 웹 서비스 구조

> Web Service Broker (웹 서비스 중개자)

- `서비스 등록 및 검색, 저장, 관리하는 주체`
- 제공자와 사용자 사이에서 사용자가 검색할 수 있게 제공

<br>

> Web Service Porvider (웹 서비스 제공자)

- `웹 서비스를 구현하여 운영하고 제공하는 주체`
- UDDI 레지스트리에 사용 가능한 웹 서비스 등록

<br>

> Web Service Consumer (웹 서비스 사용자)

- `웹서비스 제공자를 통해 웹 서비스 요청하는 주체`
- 서비스 브로커를 통해 웹 서비스 위치를 찾고 사용


[![soap-s2](/assets/img/devlog/201907/soap-s2.png)]()


## publish (등록)

- 서비스 제공자는 서비스 브로커(UDDI)에 사용 가능한 웹 서비스 `등록(publish)`

## Find (탐색)

- 웹 서비스 사용자는 원하는 서비스 위해 서비스 브로커(UDDI)를 `탐색(Find)`

## Bind (연결)

- 웹 서비스 사용자는 원하는 웹 서비스에 접근 및 호출을 위해 웹 서비스 제공자를 통해 요청
  + 1 .서비스 요청자는 서비스 제공자의 WSDL 파일을 다운로드 한 후
  + 2 . 서비스의 인터페이스에 맞게 SOAP 전송 프로토콜을 통해 서비스를 요청
  + 3 . 서비스 제공자는 요청에 따라 응답 내용울 `바인딩(Bind)`

<br><br>

---
참고:  
- [https://ko.wikipedia.org/wiki/SOAP](https://ko.wikipedia.org/wiki/SOAP)   
- [https://www.slideshare.net/yjaeseok/soap-rest](https://www.slideshare.net/yjaeseok/soap-rest)  
- [https://soul0.tistory.com/184](https://soul0.tistory.com/184)  