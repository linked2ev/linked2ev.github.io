---
layout: post
title:  "[WEB] Web Service 란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

웹 서비스(Web Service)란?


<br>


# 1. 웹 서비스(Web Service)란?

네트워크 상에서 서로 다른 종류의 컴퓨터들 간에 상호작용을 위한 소프트웨어 시스템이다. `서비스 지향`적 분산 컴퓨팅 기술의 일종으로 메시징에는 주로 `XML`로 사용되는 `SOAP, WSDL, UDDI` 등의 표준 기술로 이루어진다.

```
독립된 다양한 플랫폼, 서로 다른 디바이스 그리고 이미 구축 된 기존 시스템(Legacy System)에 따른 인프라와 비용을 절감하기 위해
```

즉, 서로 다른 이기종 간에 XML 메시징으로 상호작용한는 것이다.

<br>


> SOAP (Simple Oject Access Protocol)

HTTP, HTTPS, SMTP 등을 통해 XML 기반의 메시지를 컴퓨터 네트워크 상에서 `메시지를 교환하기 위한 통신규약 프로토콜`이다. 일반적으로 SOAP를 지원하는 서버가 대중화 되면서 대부분 웹에서 access가 가능해졌다.

<br>


> WSDL (Web Service Description Language)

웹 서비스에서 제공하는 기능들을 외부에서 이용할 수 있도록 그 사용방법을 알려주는 인터페이스 언어로 XML 기반으로 작성된 파일이다. 서로 다른 이기종 간에 웹 서비스를 제공하기 위해 `약속된 구문으로 인터페이스의 정보를 기술하는 것`이다.

웹 서비스의 URL정보, GET/POST, MIME 등 SOAP 메시지 전송을 위해 프로토콜 정보을 기술한다.

<br>


> UDDI (Universer Description Discovery and Integration)

개방형 표준과 비독점적 기술을 기반으로 개발된 전역 비즈니스 레지스터리이며, 웹 서비스를 등록하고 검색할 수 있도록 하는 일종의 `웹 서비스 전용 검색엔진`이다.

<br><br>


하여튼 우리가 흔히 접하는 웹 사이트 혹은 웹 개발 시에 서버에 데이터를 기술하고 웹 서비스를 호출하여 요청 응답을 하는 것이다.

해당 `SOAP, WSDL, UDDI` 키워드는 알고 있으면 좋을 듯 하다.

<br><br>

---
참고:  
- [https://ko.wikipedia.org/wiki/SOAP](https://ko.wikipedia.org/wiki/SOAP)  
- [https://ko.wikipedia.org/wiki/WSDL](https://ko.wikipedia.org/wiki/WSDL)  
- [https://ko.wikipedia.org/wiki/UDDI](https://ko.wikipedia.org/wiki/UDDI)  
- [http://api.epeople.go.kr/guide/](http://api.epeople.go.kr/guide/)