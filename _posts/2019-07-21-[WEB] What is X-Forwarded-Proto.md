---
layout: post
title:  "[WEB] X-Forwarded-Proto (XFP) 이란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

웹 개발을 하다보면 접하는 이슈인 `X-Forwarded-Proto`이란 키워드이다.


<br>


# 1. X-Forwarded-Proto(XFP) 란?

X-Forwarded-Proto (XFP) 헤더는 클라이언트가 프록시 또는 로드 밸런서에 접속하는데에 사용했던 프로토콜(HTTP 또는 HTTPS)이 무엇인지 확인하는 사실상의 표준 헤더 입니다. 

<br>

## 클라이언트 -> Proxy 서버 -> 웹 서버 (웹 사이트)

<br>

그래서 `클라이언트`의 HTTP/HTTPS 정보를 `프록시`에서 X-Forwarded-Proto 요청 헤더를 담아 `웹 서버`로 전달해줘야 한다. 만약 설정하지 않으면 클라이언트는 HTTPS로 접속을 하지만 웹 서버(웹 어플리케이션에서)에서는 프록시에서는 HTTP로 요청하기 때문에 클라이언트의 정보와 틀릴 수 있다.

AWS로 Elastic Load Balancer(ELB)로 Instance 인프라를 구성 할 경우, 클라이언트와 로드밸런서 간의 사용된 프로토콜을 확인하기 위해서 Apache/Nginx 에서  X-Forwarded-Proto으로 리디렉션(Redirection)를 설정해줘야 한다.



<br><br>

---
참고:  
[https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Forwarded-Proto](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Forwarded-Proto)  
[https://www.lesstif.com/pages/viewpage.action?pageId=20775886](https://www.lesstif.com/pages/viewpage.action?pageId=20775886)  
[https://meetup.toast.com/posts/163](https://meetup.toast.com/posts/163)  
[자바 - 클라이언트 실제 접속 IP 가져오기](https://linked2ev.github.io/java/2019/05/22/JAVA-1-java-get-clientIP/)
