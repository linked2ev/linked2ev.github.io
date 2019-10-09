---
layout: post
title:  "[WEB] REST는 웹의 독립 된 진화"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---


RESTful과 REST API에 대한 키워드 잠시 넣어두고, `REST`라는 키워드로만 접근해야 한다.

<br><br>



예전에는 어플리케이션의 클라이언트의 `FrontEnd` 영역인 vue/nuxt 카테고리를 작성할 때 ["Javascript 웹 서비스 발전"](https://linked2ev.github.io/devlog/2018/11/14/WEB-Web-Javascript-History/)이라는 포스팅을 작성했다. 시간이 많이 흘렀지만, 이어서 FrontEnd와 BackEnd 서버가 분리되어 독립 된 아키텍쳐 `REST`에 대해서 정리 할 예정이다.

<br>


# 웹 서비스 기술은 계속 `진화`

예전에 스마트폰이 나오면서 시대적 변화에 따라 스마트폰을 통한 사용자들이 서비스에 접근이 쉬워지고 웹앱이나 앱으로 많은 시스템들이 대체가 되고 새로운 서비스들이 나왔다. 이렇게 타 이종 간 애플리케이션 간의 소통을 위해서 웹 서비스 기술 이 주목받게 되었다.

그리고 최근 몇 년 동안 이세돌 기사와 알파고의 대국 그리고 비트코인이 이슈로 뜨면서 4차 산업 4차 산업하면서 개발자들 뿐만 아니라 Python, Deep learning, Machine learning, AI, 블록체인, 빅데이터, 5G, 자율주행, 스마트차, 스마트시티 등 키워드 얘기를 하는 것 같다.  
아마도 양자학, 디스플레이, 그리고 대용량 데이터 등을 기술 및 하드웨어적으로 뒷 받침 해줄 수 있을때 갑자기 코 앞에 다 같이 손잡고 올 것 같다.

위에 4차 산업이라는 키워드들이 오는 순간 기존 보다 더욱 이 많은 것들을 관리하기 위해 기업과 기관 내의 관리시스템과 그걸 연계하기 위한 시스템 그리고 자신들의 서비스를 사용자들에게 OPEN API를 통해 데이터를 공개할 것이다. 그리고 이에 맞는 보안과 권한방식이 필요로 할거고 이 모든 것들을 만들기 위한 형상관리 툴과 협업툴 서비스가 계속 중요해지면서 웹 서비스 또한 기술 트렌드와 유행은 바뀔 수 있지만 어떤 방식으로도 계속 `발전하고 진화` 할 것이다.

<br><br>


# `독립` 된 웹 서비스

- 애플리케이션들 분리 및 통합
- 클라이어트와 서버간 의존성이 낮은 느슨한 결합으로 확장에 용이
- 어떤 형태로든 클라이언트 디바이스와 통신할 수 멀티플랫폼에 대한 지원
- 여러 기업과 기관들의 서비스와 데이터를 주고 받을 수 있는 OPEN API연동
- MSA 등 분산 시스템과 적합함


`인터넷이 가능한 웹 서비스 내`에서는 클라이언트가 어떤 디바이스이던 그리고 클라이언트나 서버가 각각 어떤 개발언어이던지 기술이 발전을 해도 서로 같이 업데이트 할 필요가 없다. 결국은 클라이언트와 서버가 각각 기술과 언어 등이 기술이 발전을 해도 `독립 된 진화`을 한다.

<br><br>


# 그리고 `REST`

<br><br>

## REST란?
---

REST란 `RE`presentational `S`tate `T`ransfer(표현 상태 전이)의 약자로, HTTP의 창시자 중 한 사람인 Roy Fielding(로이 필딩)의 2000년 박사학위논문에 의해서 소개되었다. 

REST는 웹의 장점을 살려 HTTP프로토콜 기반으로 의도에 맞게 필요한 웹의 고유한 자원(URI)에 접근하는 방식인 하이퍼미디어 기반 `분산 시스템을 설계하기 위한 아키텍처 스타일`이다.

아키텍처 스타일은 `제약 조건의 집합`이며, 아래의 조건을 따른다.

- `Client/Server`
- `Uniform Interface`
- `Layered System`
- `Cacheable`
- `Stateless`
- `Code-on-demand`

<br>


> `Client/Server` (클라이언트/서버)
---

웹의 일관된 인터페이스를 따른다는 전제하에 REST 서버는 API 제공, 클라이언트는 사용자 인증이나 컨텍스트(세션, 로그인 정보)등 역활은 분명하게 나누고, 클라이언트와 서버의 관심사의 분리로 서로간 의존성이 줄어든다. 

그래서 클라이언트와 서버는 각자의 언어 및 기술을 사용하여 독립적으로 구현되고 배포될 수 있다.

<br>


> `Uniform Interface` (인터페이스 일관성)
---

URI로 지정한 Resource에 대한 조작을 통일되고 일관성 있는 인터페이스로 수행하며, HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 특정 개발언어나 기술에 종속되지 않는 느슨한 결합이다.

그래서 아키텍처를 단순화시키고 작은 단위로 분리가 가능하기에 클라이언트-서버의 각 파트가 독립적으로 개선 할 수 있다.

REST 일관성 있는 `인터페이스의 4가지 원칙`에 대한 가이드

1. 리소스 식별
2. 표현을 통한 리소스 처리
3. 자기-서술적 메시지 (Self-descriptive messages)
4. 애플리케이션 상태 엔진으로서의 하이퍼미디어(HATEOAS)

정말 로이 필딩이 추구하는 REST를 따르겠다면 Self-descriptive messages와 HATEOAS를 만족해야 한다고 하며 그렇지 않으면 HTTP API, WEB API로 칭하거나 그냥 REST API로 부를 수 있다.

<br>


> `Layered System` (계층 시스템)
---

웹의 일관된 인터페이스를 사용해서 서버는 다중 계층으로 구성될 수 있으며 비즈니스 로직을 수행하는 API 서버 앞단에서 사용자 인증, 암호화, 로드밸런싱, 캐싱, 프록시 등 계층을 추가해서 구조상의 유연성을 둘 수 있다.

예를 들어 프락시 또는 게이트웨이 같은 네트워크 기반의 중간매체를 사용해서, 클라이언트는 필요한 자원에 대해서 요청하지만 중계서버에 의해 API 서버에 호출하는 개념이다.

마이크로 서비스 아키텍쳐에서 API 게이트웨이나 프록시서버 또는 아파치에서 리버스 프록시 서버 등을 구축해 보안을 강화하거나 부하를 분산하는 용도로 사용한다.

<br>


> `Cacheable` (캐싱 처리)
---

REST의 큰 특징 중의 하나는 HTTP라는 기존의 웹 표준을 그대로 사용하기 때문에, 웹에서 사용되는 기술인 캐시를 활용이 가능하다.

캐시라는 제약조건에 의해 웹 서버가 응답 데이터마다 캐시 여부를 선언한다. 캐싱 응답 데이터는 전체 응답시간, 성능, 서버의 자원 이용률을 향상시키며 이용 가능성과 안정성을 향상시키며, 웹 서버의 부하를 줄일 수 있다.

<br>


> `Stateless` (무상태)
---

HTTP 프로토콜은 Stateless Protocol이므로 REST도 무상태성이며 상태 정보를 유지하지 않는다. 그래서 클라이언트의 context를 서버에 유지하지 않으며, REST API서버에서는 세션 같은 context 정보를 신경쓸 필요가 없기 때문에 구현이 단순하며 더욱 클라이언트의 요청의 비즈니스 로직만 처리한다.

<br>


> `Code-on-demand` (주문형 코드)`
---

웹은 주문형 코드 Code-On-Demand 를 아주 많이 사용한다. 서버에서 코드를 작성해서 클라이언트에서 실행할 수 있도록 한다. 예를 들어 서버에서 작성해서 자바스크립트를 실행시키는걸 말한다.

<br><br>


## 결론은 `"REST는 웹의 독립적 진화"`을 위한 분산 시스템 아키텍처 스타일 이다.

<br><br>

이렇게 `REST`라는 키워드에 대해 알아보았다. 이제 다음 포스팅에서 RESTful 과 REST API에 대해서 정리할 예정이다.

<br>

마지막으로 REST 창시자 로이필딩이 한 말을 적고 마무리 짓겠다.


> REST emphasizes evolvability to sustain an uncontrollable system. If you think you have control over the system or aren’t interested in evolvability, don’t waste your time arguing about REST.
<br><br> - Roy T. Fielding

---

> 시스템 전체를 통제할 수 있다고 생각하거나, 진화에 관심이 없다면, REST에 대해 따지느라 시간을 낭비하지 마라
<br><br> - 로이 토마스 필딩

<br><br>

---
[참고]

- [wikipedia - REST](https://ko.wikipedia.org/wiki/REST)
- [Naver d2 - 그런 REST API로 괜찮은가](https://www.youtube.com/watch?v=RP_f5dMoHFc)
- [REST API의 이해와 설계-#1 개념 소개 - 조대협](https://brainbackdoor.tistory.com/53)
- [https://brainbackdoor.tistory.com/53](https://bcho.tistory.com/953)
- 스프링을 이용한 RESTful 웹 서비스 구축하기 - 책
- REST API 디자인 규칙 - 책
