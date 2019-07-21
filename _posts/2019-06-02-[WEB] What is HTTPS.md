---
layout: post
title:  "[WEB] HTTPS 이란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

HTTPS 이란?


<br>


# 1. HTTPS란?

> `HTTPS = HTTP + SSL`

- HTTP = Hyper Text Transfer Protocol
- HTTPS = HTTP + S(over Secure socket layers)
- SSL(Secure Socket Layer)

HTTPS는 TCP가 아닌 HTTP 통신을 하는 소켓 부분을 SSL(Secure Socket Layer)이나 TLS(Transport Layer SEcurity)이라는 프로토콜로 통해 SSL을 사용함으로써 암호화, 증명서, 완전성 보호를 할 수 있다.

<br>

> 최근 들어서 보안 이슈 때문에 모든 웹사이트들이 https로 적용하고 있다. 예를 들어 2019년 초에 티스토리 서비스 내에도 SSL 적용한다는 공지가 있었다. 새로운 웹 프로젝트들은 웹 사이트 내 모든 주소 url를 https로 처리하고 있는 추세이다.<br><br>
구글 내 사용자 API 서비스 등도 https를 요구하고 있으며, PWA(프로그레시브 웹 앱)와 같은 새로운 기술 또한 https만 사용이 지원이 가능하다고 한다.


<br><br>


---
참고:  
[https://blog.sonim1.com/99](https://blog.sonim1.com/99)
