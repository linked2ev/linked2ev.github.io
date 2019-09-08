---
layout: post
title:  "[Spring] DAO / DTO(VO)"
subtitle:   "[spring]"
categories: spring
tags: spring-posting
comments: true
---

DAO / DTO(VO)

<br>


# DAO (Data Access Object)

- 실제로 커넥션을 통해 DB에 접근하는 객체
- Connection Pool로 DB에 연결한 Connection을 통해서 사용자의 여러 요청을 효율적으로 커넥션 관리와 보안관리에 용이하다.
- 데이터베이스 액세스를 DAO에서만 하게 되면 다수의 원격 호출을 통한 오버헤더를 VO나 DTO를 통해 줄일 수 있고, 다수의 DB 호출 문제를 해결할 수 있다. 또한 단순히 읽기만 하는 연산이므로 트랜젝션 간의 오버헤드를 감소할 수 있다.

<br><br>


# DTO(Data Transfer Object)

- Presentation Layer, Business Layer, Persistent Layer 계층간 데이터 교환을 위한 자바빈즈를 말한다.
- Data란 일반적인 Database도 될 수 있고, 파일, 메모리 또는 기타 다른 저장소도 될 수 있다.
- 비 서버 측 클라이언트도 네트워크 오버헤드 없이 영속성 데이터에 액세스 할 수 있다는 점입니다
- 인터페이스를 통한 메서드 잦은 호출에 의해 전송될 데이터를 DTO을 이용해서 한번만 호출하게 해서 네트워크의 오버헤더를 줄일 수 있다.    

<br><br>


# VO (Value Object)

- 데이터 그 자체로 의미 있는 것을 담고 있는 객체이다.
- VO는 DTO와 동일한 개념이지만 read only 속성을 갖는다.
- 관계데이터베이스의 레코드에 대응되는 자바클래스이다.

<br><br>


---
[참고]
- https://m.blog.naver.com/PostView.nhn?blogId=ljc8808&logNo=220462395989&proxyReferer=https%3A%2F%2Fwww.google.com%2F
- https://itmore.tistory.com/entry/자바-VO-DTO-차이점사용하는-방식이-같다고-똑같다고-생각하지-말자