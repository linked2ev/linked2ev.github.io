---
layout: post
title:  "[WEB] 웹 서비스 SOAP 와 REST"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

웹 서비스 종류에는 방식에 따라 SOAP 그리고 REST 가 있다. 


<br>

이번 포스팅은 SOAP와 REST 방식 웹 서비스에 대한 글이다. 뒤로는 아마도 REST에 대한 글을 작성할 듯 하다.

해당 포스팅을 가볍게 읽기전에 간접적으로 도움되는 포스팅들 입니다.

- [HTTP란?](https://linked2ev.github.io/devlog/2019/06/01/WEB-What-is-HTTP/)
- [URL과 URI 설명](https://linked2ev.github.io/devlog/2019/05/05/WEB-URL-vs-URI-difference/)
- [웹 서비스란?](https://linked2ev.github.io/devlog/2019/07/28/WEB-What-is-Web-Service/)
- [SOAP란?](https://linked2ev.github.io/devlog/2019/07/29/WEB-What-is-SOAP/)

<br>


# SOAP(Simple Object Access Protocol)

SOAP는 SOA를 따르는 웹 서비스 디자인 표준이다.

HTTP, HTTPS, SMTP 등을 통해 `XML 기반`의 메시지를 컴퓨터 네트워크 상에서 `메시지를 교환하기 위한 통신규약 프로토콜`이다.

- 프록시와 방화벽에 제약이 없다.
- 플랫폼과 프로그래밍에 독립적이다.
- WSDL, UDDI 등 표준 통신규약이 있다.
- 분산 환경에 적합하다.
- 기존의 컴포넌트보다 이기종간 상호운용성이 높음
- SOAP는 REST에 비해 복잡한 구조로 무겁고 느리다.

<br>

## SOA (Service Oriented Architecture)
---

- `서비스` 지향 아키텍처

[![soap-s1](/assets/img/devlog/201908/soap-s1.png)]()

<br>

[![soap-s1](/assets/img/devlog/201908/soap-s2.png)]()

<br>

### SOAP는 `SOAP메시지`를 통해서만 `서비스`를 요청하고 반환하다.


<br>

# REST(REpresentational State Transfer)

REST는 ROA를 따르는 웹 서비스 디자인 표준이다.

HTTP의 기본 개념과 사상에 따른 웹 서비스이다. REST 아키텍처에 따라 구현 된 응용프로그램을 RESTful 웹 서비스라 칭함.

- 플랫폼과 프로그래밍 언어에 독립적이다.
- SOAP의 UDDI와 같은 중개자를 통하지 않아 빠르다. 
- SOAP보다 구조가 단순하다.
- point-to-point 통신 모델이라서 상호작용하는 분산환경에는 유용하지 않음
- 보안, 정책에 대한 표준이 없다.
- HTTP통신 모델에서만 메서드를 통해 리소스를 요청한다.
- 리소스들은 고유의 URI를 가진다.

<br>

## ROA (Resourece Oriented Architecture)
---

`리소스` 지향 아키텍처

[![rest-s1](/assets/img/devlog/201908/rest-s1.png)]()

<br>


### REST는 HTTP 프로토콜의 메서드 `GET/POST/PUT/DELETE`로 `URI`를 통해 서비스 제공자에게 직접 서비스 요청 후, 서비스 제공자가 다양한 형태(JSON, XML, RSS 등)로 `resource` 반환한다.

<br>

> SOAP는 서로 다른 기종간에 통신을 위해 표준 규약 등을 알아야 하고 구조가 복잡하고 무겁지만, `REST는 리소스 중심` 웹 서비스이다.<br><br>개인적으로 개발자가 아닌 분들에게는 REST란 쉽게(?) 데이터 중심 서비스라고 말한다. 하지만, REST에서는 실제로 `리소스가 데이터를 의미 하지 않는다.` 하여튼 오늘도 WEB 관련 포스팅은 천천히 목적지를 위해 작성 끝.

<br>

---

참고
- https://www.slideshare.net/seunghochoi4/soap-restful
- https://www.slideshare.net/yjaeseok/soap-rest