---
layout: post
title:  "[WEB] Protocol(프로토콜) 이란?"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

네트워크에 대해 설명하는 카테고리가 아니기에 간단하게 프로토콜 관련해서 포스팅  


<br>


# 1. 프로토콜 이란?

통신 프로토콜 또는 통신 규약은 `컴퓨터나 원거리 통신 장비 사이에서 메시지를 주고 받는 양식과 규칙의 체계`이다. 즉 통신 규약 및 약속이다.  

그리고 통신 프로토콜은 신호 체계, 인증, 그리고 오류 감지 및 수정 기능을 포함할 수 있다. 가장 일상적으로 사용되는 프로토콜 세트 중 하나가 TCP/IP이다. 거의 `모든 컴퓨터가 기본으로 제공하는 인터넷 표준 프로토콜이다.`

<br><br>


# 2. TCP/IP

> IP(Internet Protocol)

- 네트워크 계층에서 동작하는 프로토콜
- 패킷 통신 방식의 인터넷 프로토콜인 IP  
- IP는 패킷 전달 여부를 보증하지 않고, 패킷을 보낸 순서와 받는 순서가 다를 수 있다.

<br>


> TCP(Transmission Control Protocol)

- 전송 계층에서 동작하는 프로토콜
- 전송 조절 프로토콜인 TCP
- TCP는 IP 위에서 동작하는 프로토콜로, 데이터의 전달을 보증하고 보낸 순서대로 받게 해준다. 
- HTTP, FTP, SMTP 등 TCP를 기반으로 한 많은 수의 애플리케이션 프로토콜들이 IP 위에서 동작하기 때문에, 묶어서 TCP/IP로 부르기도 한다.


<br>


# 3. OSI 7계층

- OSI (Open Systems Interconnection Reference Model)
- 국제 표준화 기구(ISO)에서 개발한 모델
- 컴퓨터 네트워크 프로토콜 디자인과 통신을 계층별로 나눈 형태
- OSI 모델을 OSI 7 Layer 또는 OSI 7 계층 모형이라고 칭함
- (물데네전세표현응..)

1. 물리 계층
2. 데이터 링크 계층
3. 네트워크 계층
4. 전송 계층
5. 세션 계층
6. 표현 계층
7. 응용 계층


<br><br>



# 4. 응용계층 프로토콜

<br>

## ​FTP (File Transfer Protocol) - Data(20), Control(21)
--- 

​FTP는 파일 전송을 위한 가장 기본 프로토콜로, RFC 114fh 1971년에 만들어졌다.

당시는 IP와 TCP가 존재하지도 않을 때다. 1972년에 텔넷(Telnet)과 함께 표준으로 재정되었다.


FTP는 대화형으로 클라이언트와 서버가 통신이 가능하다. 예를 들면, 사용자가 원격 컴퓨터의 디렉토리에 파일 목록을 문의할 수 있고, 명령어 사용법을 대화식으로 배울 수 있다. 또한 전송되는 파일의 형식을 지정할 수 있다. 사용 가능한 파일 형식은 텍스트나 바이너리 파일이고, 텍스트 파일은 ASCII나 EBCDIC 문자로 저장할 수 있다. FTP에서는 파일 전송 전에 먼저 서버에 로그인해야 하며, 사용자는 로그인 이름과 패스워드를 입력하여 클라이언트를 인증한다. 서버는 클라이언트가 검증된 사용자가 아닌 경우 파일 전송을 거부할 수 있으며, 텔넷과 유사하게 여러 클라이언트가 동시에 서버에 접근할 수 있다.

​<br><br>


## Telnet​ (23)
---

​텔넷은 사용자가 원격에 있는 서버에 로그인하도록 TCP 연결을 설정하며, 단말기가 원격 컴퓨터 바로 옆에 있는 것처럼 직접 조작할 수 있게 해준다. 

또한 원격 컴퓨터에서 수행한 결과를 단말기에 보여주는데, 이와 같은 서비스는 사용자 단말기가 원격 컴퓨터에서 직접 연결된 것 처럼 보이게 한다.

​​<br><br>
​

## ​SMTP (Simple Mail Transfer Protocol) (25)
---

​TCP/IP에서 많이 사용하는 응용 서비스 중 하나가 메일 서비스다. 메일 서비스는 빠르고 편리해 많이 사용하며, 텍스트나 실행 파일 등을 손쉽게 전송할 수 있다. 기존의 TCP/IP응용에서는 데이터를 전송하고 이에 대한 ACK로 응답하거나, 재전송을 통해 목적지까지 데이터를 직접 전송하지만, 메일 시스템에서는 원격 컴퓨터나 네트워크 연결에 장애가 발생한 경우에도 통신이 가능하다. 즉, 송신자가 전송한 메일 메시지는 목적지 컴퓨터나 네트워크의 장애가 복구될 때까지 기다리며, 장애가 복구되었을 때 메일 메시지가 배달된다. 이와 같은 전송 방법을 지연 배달(Delayed Delivery) 이라 하는데, 메일 시스템에서는 지연 배달을 휘애 스풀링(Spooling)기법을 이용한다. 사용자가 메일 메시지를 전송하면 시스템은 전송 시각, 목적지 주소, 수신자, 송신자 등의 값을 개별 스풀에 복사해둔다. 그 후 메일 시스템은 원격 컴퓨터로 메일 메시지를 백그라운드로 전송한다.

​​<br><br>


## ​DNS (Domain Name System) (53)
---

​DNS는 도메인 이름 주소를 통해 IP주소를 확인할 수 있는 프로토콜이다.

​​<br><br>
​

## TFTP (Trivial File Transfer Protocol) (69)  
---

​TFTP는 1985년에 만들어졌으며 FTP처럼 파일을 전송하기 위한 프로토콜이다. 하지만 UDP 패킷을 사용하고, FTP와 달리 인증 기능을 제공하지 않는다.

​​​<br><br>
​

## ​HTTP (HyperText Transfer Protocol) (80)  
---

`HTTP(Hypertext Transfer Protocol)하이퍼텍스트 전송 규약, 서버와 클라이언트가 인터넷상에서 데이터를 주고받기 위한 프로토콜(protocol)을 말한다.`

본인이 생각하기에 `HTTP` 에 대해 알기 전에(설명하기 위해) 해당 포스팅을 통해서 간단하게라도 이정도까지 알고 가면 좋다고 생각해 포스팅한 의도이다.

​​​​<br><br>


## ​POP3 (Post Office Protocol 3, 110번) & IMAP (Internet Message Access Protocol, 143번)
---

​POP3는 메일 서버로 전송된 메일을 확인할 때 사용되는 프로토콜이다. IMAP도 POP3와 기본적으로 같으나, 메일을 읽은 후에도 메일이 서버에 남는다는 점이 다르다.

​<br><br>


## ​NetBIOS (Network Basic Input / Output System, 138)
---

​NetBIOS는 IBM에서 개발했지만 윈도우에서 차용했다. 이 프로토콜은 내부 네트워크의 프린터처럼 아주 기본적인 사무기기와 윈도우 시스템 간의 파일 공유를 위한 것이기 때문에 대부분 항상 열려 있다. 또한, 이 프로토콜은 NBT(NetBIOS over TCP)프로토콜을 사용하여 원격의 인터넷으로 전달이 가능하다.

​<br><br>


## ​SNMP (Simple Network Management Protocol, 161)
---

​SNMP는 네트워크 관리와 모니터링을 위한 프로토콜이다.​


<br><br>

---
참고:  
[https://ko.wikipedia.org/wiki/통신_프로토콜](https://ko.wikipedia.org/wiki/%ED%86%B5%EC%8B%A0_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C)  
[https://ko.wikipedia.org/wiki/인터넷_프로토콜_스위트](https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%EB%84%B7_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C_%EC%8A%A4%EC%9C%84%ED%8A%B8)  
[https://jjinfotech.tistory.com/36?category=726000](https://jjinfotech.tistory.com/36?category=726000)
[http://blog.naver.com - 응용계층](http://blog.naver.com/PostView.nhn?blogId=ugk315&logNo=70190013457&parentCategoryNo=&categoryNo=41&viewDate=&isShowPopularPosts=false&from=section)