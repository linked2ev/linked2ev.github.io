---
layout: post
title:  "[WEB] MCI, EAI, ESB 미들웨워"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

기업/기관 대규모 시스템 내부시스템에서 이기종 간에 데이터, 어플리케이션, 서비스들을 통합 및 데이터 공유를 위한 미들웨어에 대한 포스팅이다.

<br><br>


# MCI

- MCI(Multi Channel Integration)는 영업점, 인터넷 서비스, 콜센터, 단말, 자동화기기 등 기업/기관의 다양한 대외/내부 여러 `채널(엔드포인트)를 효과적으로 연계 및 통합`할 수 있도록 지원하는 `미들웨어`이다.
- 기업/기관의 내부시스템과 채널의 중간에 위치하여 로드밸런싱 등 가용성 증가
- 채널과 내부시스템 등을 독립적인 시스템이 가능하게 함으로 유연성 증가
- 이기종간(서로 다른 소프트웨어, 어플리케이션)에 요청하는 데이터 전문변환, 트랜잭션, 라우팅 역할
- MCA는 멀티 채널로 된 아키텍처를 지칭하며, MCI는 멀티 채널로 구성된 아키텍처의 통합(서비스 연계)를 지칭

> 금융권에서 MCI는 대외/내부 채널 영업점, 단말, 디지털창구, 인터넷뱅킹, 폰뱅킹, 금융공동망, 금융결제원 등 여러 대외채널증가로 채널통합관리 역할로 사용된다.

<br><br>


# EAI

- EAI(Enterprise Application Integration)는 주로 기업 내부 이기종 시스템을 연계 시키는 `Hub&Spoke` 방식인 전사적 `애플리케이션을 통합`하기 위한 `미들웨어`이다.
- `브로커(Broker), 어댑터(Adapter), 버스(Bus), 트랜스포머(Transformer)` 구성요소로 `EAI가 중개자` 역할로 `메시징`으로 각 서비스들을 `Publish(발행)/Subscribe(구독)`
	- `표준화 되지 않은 이기종 시스템(소프트웨어, 어플리케이션)간`의 연동을 가능하게 메시지 전달로부터 거래(트랜잭션)를 원자성, 일관성을 유지하기 위한 트랜잭션 네트워크를 통합 관리
	- 이기종간에 데이터 변환 기능(XML, JSON, HTTPS, JMS 등), 거래전문 변환 및 매핑 역할로 데이터 통합 관리
	- 중앙집중식이기에 EAI 시스템 벤더사에 종속적

> 금융권에서는 EAI는 수신(예금,출금,이체), 여신(상품,대출), 대행, 외환 등 거래 중심 업무가 중요하고 서로 다른 레거시 시스템들이 많기 때문에 계정계, 내부시스템에서 사용된다.

<br><br>


# ESB

[![intellij-vm-profiles-active-s1](/assets/img/2022/velog_eai-s2.png)]() <br>


- ESB(Enterprise Service Bus)는 서비스들을 상호연동을 구현하는 `서비스 중심`으로 메시지방식을 이용한 `Backbone 역할로 SOA를 지원하는 미들웨어`이다.
- 기존에 있는 많은 레거시 시스템들과 새로 생기는 시스템들이 많아지고 이기종간의 통합이 많아지다 보니 이를 아에 표준화하고자 하는 아키텍쳐가 SOA (Service Oriented Archtiecture: 서비스 지향 아키텍쳐) 이다.
	- 웹서비스 기반으로 통신이 표준화되었기 때문에 독립 된 서비스들간의 통신에 EAI처럼 별도의 어댑터가 불필요
	- 각 시스템들이 서로 다른, 새로운 기술과 개발언어로 서비스가 구축이 되어도 각 서비스들은 `독립 된 진화`을 한다.


> 많은 웹 서비스들은 웹표준 기술에 맞춰 독립 된 서비스로 발전하다 보니 대규모 웹 서비스들은 자연스레 MSA방식을 채택하게 된다. [[참고 - 독립 된 웹 서비스]](https://linked2ev.github.io/devlog/2019/10/06/WEB-REST-is-the-independent-evolution-of-the-web/)
그래서 기업/기관 내부시스템들은 ESB 방식으로 상대적으로 적용하기 어렵고 필요성도 없지 않을까

<br><br>


---
[참고]
- https://itwiki.kr/w/시스템_연계_솔루션
- http://www.bikorea.net/news/articleView.html?idxno=6189
- http://m.bikorea.net/news/articleView.html?idxno=21715
- http://www.bikorea.net/news/quickViewArticleView.html?idxno=19622
- http://www.dator.co.kr/yjna/textyle/90302
- https://linked2ev.github.io/devlog/2019/10/06/WEB-REST-is-the-independent-evolution-of-the-web/