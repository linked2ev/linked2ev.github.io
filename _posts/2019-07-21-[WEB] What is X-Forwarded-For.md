---
layout: post
title:  "[WEB] X-Forwarded-For (XFF) 이란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

웹 개발을 하다보면 접하는 이슈인 `X-Forwarded-For`이란 키워드이다.


<br>


# 1. X-Forwarded-For(XFF) 란?

XFF는 HTTP Header 중 하나로 HTTP Server에 요청한 Client의 IP를 식별하기 위한 표준입니다.

`웹 서버나 WAS 앞에 L4 같은 Load balancers 나 Proxy server, caching server 등의 장비가 있을 경우 웹서버는 Proxy server 나 장비IP에서 접속한 것으로 인식`합니다.
그렇기 때문에 웹서버는 실제 클라이언트의 정보가 아닌 중개역할을 하는 Proxy서버 IP를 요청한 IP로 인식하고, Proxy장비 IP 로 웹로그를 남기게 됩니다.

<br>

## 클라이언트 -> Proxy 서버 -> 웹 서버 (웹 사이트)

<br>

Proxy서버는 네트워크 중간에 위치하여 중개역할로써 Cache 등의 기능을 활용하여 
네트워크 트래픽을 줄이거나 자원의 절약을 위해서 사용된다.

그래서 클라이언트는 Proxy서버를 거쳐서 웹 서버에 가기 때문에 클라이언트의 IP정보를 Proxy서버에서 웹 서버로 요청할 때 X-Forwarded-For를 통해 보낸다.

그러기 위해서는 `Apache/Nginx 등 httpd 웹 서버에서 X-Forwarded-For를 따로 설정`해야 한다.


<br><br>

---
참고:  
[https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Forwarded-For](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Forwarded-For)  
[https://www.lesstif.com/pages/viewpage.action?pageId=20775886](https://www.lesstif.com/pages/viewpage.action?pageId=20775886)  
[https://meetup.toast.com/posts/163](https://meetup.toast.com/posts/163)  
[자바 - 클라이언트 실제 접속 IP 가져오기](https://linked2ev.github.io/java/2019/05/22/JAVA-1-java-get-clientIP/)
