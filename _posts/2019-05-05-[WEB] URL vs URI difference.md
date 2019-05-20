---
layout: post
title:  "[WEB] URL과 URI 설명"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

URL과 URI 설명

<br>


# 1. URL과 URI 개념

어느 사이트 주소를 말할 때 URL이라고 말한다. 그리고 URI라는 용어도 들어봤을 것이다. 몸을 담고 있는 웹서비스 방식이나 개발환경에 따라 해당 포스팅에 대한 키워드 검색 관심도가 다를듯 하다. 본인은 예전에 개발 시 request 경로 정도로만 생각했다.

<br>

내용이 길면 힘들다. 3가지 키워드만 기억만 하자

- `URL : 리소스 위치`
- `URI : 리소스 식별`
- `URN : 리소스 이름`

<br>

## URL : Uniform Resource Locator
---

- `통합 자원 지시자`(Uniform Resource Locator, URL)은 네트워크 상에서 자원이 어디에 위치해 있는지를 알려주기 위한 규약이다. 
- [위키 참고: URL](http://ko.wikipedia.org/wiki/URL)

<br>

## URI : Uniform Resource Identifier
---

- `통합 자원 식별자`(Uniform Resource Identifier, URI)는 인터넷에 있는 자원을 나타내는 유일한 주소이다.
- [위키 참고: URI](http://ko.wikipedia.org/wiki/URI)



<br><br>


# 2. URL 과 URI 서비스 요청 비교

예를 들어 

## 우리가 쉽게 접하는 스프링 MVC패턴 방식인 웹서비스를 생각해보자.

```txt
http://shop.com/goods/detail.do?goodsno=77

// 또는
detail.asp or detail.jsp
```

위의 URL로 요청을 하면 서버에서 `서버/goods/detail 디렉터리 경로에 해당하는 jsp 파일`을 WAS에서 자바소스로 컴파일 한 후 클라이언트 요청에 응답하는 것이다. 
(jsp, html, img, pdf 등을 요청한다.)

여기서 detail.jsp 자원의 위치를 가리키면서 식별 할 수 있기 때문에 `URL이면서 URI이다.`

<br>


## 그리고 REST방식(?) 요청 웹서비스와 비교해보자

```txt
http://shop.com/goods/detail
```

위에 URL은 동일하게 `서버/goods/detail` 디렉터리 경로에 해당하는 jsp 파일을 가져 올 수는 있을 것이다.

하지만, 원하는 상품의 정보를 가져올 수는 없다.
가져오기 위해서 아래와 같이 상품번호인 `77`식별자(Identifier)와 같이 요청해야 서비스를 얻을 수 있다.

```txt
http://shop.com/goods/detail/77
```

실제로 해당 웹사이트의 goods/detail/77 라는 파일은 없고, 식별자를 통해 서비스를 제공 받는 개념이다. `그래서 URI는 URL은 아니다.`


결론적으로 URL은 URI이고, URI는 URL은 아니기에  
`URL은 URI을 포함하는 상위개념이다.(URL (URI))`

<br><br>

다른 방식으로 다시한번 설명하면
아래와 URL로 요청을 했을 경우 HttpServletRequest 객체 내 함수로 비교하면

```txt
http://shop.com/goods/detail.do?goodsno=77
http://shop.com/goods/detail/77
```

getRequestURL는 쿼리스트링을 제외한 프로토콜 + 도메인 + 포트번호 + 컨텍스트경로 + 서블릿경로를 알 수 있고, getRequestURI는 현재 도메인에서 리소스를 식별할 수 있는걸 알 수 있다.


```java
// - URL
// http://shop.com/goods/detail.do
// http://shop.com/goods/detail/77
req.getRequestURL();

// - URI
// /goods/detail.do
// /goods/detail/77
req.getRequestURI(); 
```

<br><br>

URN이라는 것도 있다. 이부분에 대해서는 본인도 완전하게 이해한 상태가 아니기에 포스팅은 안하겠습니다. 다른 개발자분들의 포스팅을 참고하시길 바랍니다~

<br><br>

> 개인적으로 WEB 카테로그에 들어갈만 한 개념이나 내용들 만큼은 중요하다고 생각해서 지속적으로 정리하고 포스팅하려고 했으나 이것 저것 하다보니 쉽지가 않다.

<br><br>


---
참고:  
[https://cocomo.tistory.com/209](https://cocomo.tistory.com/209)  
[https://codedragon.tistory.com/3509](https://codedragon.tistory.com/3509)  
[stackoverflow.com - URL/URI/URN](https://stackoverflow.com/questions/176264/what-is-the-difference-between-a-uri-a-url-and-a-urn)