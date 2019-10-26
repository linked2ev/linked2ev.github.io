---
layout: post
title:  "[WEB] RESTful과 REST API"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

["REST"](https://linked2ev.github.io/devlog/2019/10/06/WEB-REST-is-the-independent-evolution-of-the-web/)에 이어서 그래서 `"RESTful"`과 `"REST API"`는?

<br><br>

# REST

REST는 웹의 장점을 살려 HTTP프로토콜 기반으로 의도에 맞게 필요한 웹의 고유한 자원(URI)에 접근하는 방식인 하이퍼미디어 기반 `분산 시스템을 설계하기 위한 아키텍처 스타일`이다.

아키텍처 스타일은 `제약 조건의 집합`이며, 아래의 조건을 따른다.

- `Client/Server`
- `Uniform Interface`
- `Layered System`
- `Cacheable`
- `Stateless`
- `Code-on-demand`

<br>

# RESTful

RESTful은 위의 `아키텍쳐 스타일의 제약 조건의 집합을 모두 만족하는 것`을 의미한다.

REST의 특징을 지키는 API를 `RESTful하다` 라고 표현한다. 그리고 RESTful하게 만든 서버를 REST API서버라 칭한다. 반대로 REST API를 제공하는 웹 서비스를 RESTful하다고 할 수 있다.

<br><br>


# REST API

RESTful하게 만든 서버를 REST API서버라고 한다. 그러면 REST API란 무엇인가

> API 의미

API(Application Programming Interface)는 응용 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다. 

보통 API는 데이터와 기능을 제공하여 프로그램 간 상호작용하며 OpenAPI(공공 데이터,구글API), 마이크로 서비스(기능별 모듈 어플리케이션)에서 제공하는 업체 대부분은 REST API 형태로 웹 서비스를 제공한다.

<br><br>

> REST 구성 요소

- HTTP URI : 자원(Resource)
- HTTP Method : 행위(Verb)
- MIME Type : 표현 방식(Representation of Resource)

<br>

결국 `리소스 중심으로 URI를 설계하며, 해당 자원에 기능에 따라 HTTP메서드인 GET, POST, PUT, DELETE, HEAD, OPTIONS 로 정의해서 RESTful하게 설계한 API서버를  REST API라고 말한다.`

<br><br>


## 그래서 REST 웹 서비스 개발은?

REST는 웹 서비스를 구축하고 사용하기 쉽기 때문에 웹에서 웹 서비스를 구축하기위한 사실상의 표준이 되가고 있고 RESTful 웹 서비스를 빠르게 지원하기 위한 많은 프레임워크와 라이브러리가 있다.

자바 진영에서 2007년에 REST 방식 웹 서비스를 위해 JSR-311를 따르는 JAX-RS, Restlet, Jersey, RESTEasy, Apache CXF 등 애너테이션 기반으로 빠르게 구현을 지원하는 라이브러리다.

스프링 진영에서도 Spring Web MVC에서는 자바진영의 JSR-311를 따르지는 않지만, REST 기반의 웹 서비스 개발에 필요한 기능을 대부분 지원하며 스프링4부터 REST 웹 서비스 개발을 쉽게하기 위해 @RestController 등 애너테이션 기반 구현을 지원하게 릴리즈 되었다.

결론은 `개발자들은 자바, 스프링 등 개발언어에 따라 RESTful 한 설계 된 웹 서비스를 개발 할 때 경량화된 프레임워크나 라이브러리를 이용하는 것이다.`

<br><br>


---
[참고]

- [wikipedia - REST](https://ko.wikipedia.org/wiki/REST)
- [Naver d2 - 그런 REST API로 괜찮은가](https://www.youtube.com/watch?v=RP_f5dMoHFc)
- [REST API의 이해와 설계-#1 개념 소개 - 조대협](https://brainbackdoor.tistory.com/53)
- [https://brainbackdoor.tistory.com/53](https://bcho.tistory.com/953)
- 스프링을 이용한 RESTful 웹 서비스 구축하기 - 책
- REST API 디자인 규칙 - 책
