---
layout: post
title:  "1. 스프링부트 MVC 시작하기"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

Springboot MVC 시작하기

<br>


# 개발 환경 및 내용

- OS: Window 10
- Dev Tool: Eclipse IDE 2019-06
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.7
- Build Tool: Gradle v3
- RDBMS: MariaDB
	+ DBCP: hikariCP
	+ Persistence Framework: MyBatis
- #Lombok #Log4j2 #DAO #Mapper #Filter #Interceptor #AOP

---

<br><br>

# 목차

[2 . 개발환경 설정 및 Eclipse STS Gradle 설치](https://linked2ev.github.io/gitlog/2019/08/19/springboot-mvc-2-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD-%EC%84%A4%EC%A0%95-%EB%B0%8F-Eclipse-STS-Gradle-%EC%84%A4%EC%B9%98/)
+ 개발 환경 설정
+ Eclipse에 외부프로그램 STS, Gradle 등 설치


[3. Springboot Gradle 프로젝트 생성](https://linked2ev.github.io/gitlog/2019/08/19/springboot-mvc-3-springboot-gradle-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1/)
+ 스프링부트 Gradle 프로젝트 생성하기
+ 스프링부트 Hello World 출력하기 


[4. 스프링부트 MyBatis + HikariCP + MariaDB 설정](https://linked2ev.github.io/gitlog/2019/08/21/springboot-mvc-4-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MyBatis-+-HikariCP-+-MariaDB-%EC%84%A4%EC%A0%95/)
+ Datasource 연결 설정
+ MyBatis, HikariCP, MariaDB 설정


[5. 스프링부트 HikariCP MyBatis 옵션 설정](https://linked2ev.github.io/gitlog/2019/08/22/springboot-mvc-5-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-HikariCP-MyBatis-%EC%98%B5%EC%85%98-%EC%84%A4%EC%A0%95/)
+ mybatis-config.xml로 추가 설정


[6. 스프링부트 MVC 게시판 (MyBatis Mapper 사용)](https://linked2ev.github.io/gitlog/2019/08/23/springboot-mvc-6-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-%EA%B2%8C%EC%8B%9C%ED%8C%90-(MyBatis-Mapper)/)
+ Spring MVC 개념 설명


[7. 스프링부트 Lombok(롬복) 사용하기](https://linked2ev.github.io/gitlog/2019/08/25/springboot-mvc-7-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-Lombok(%EB%A1%AC%EB%B3%B5)-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/)
+ 롬복 설치
+ 롬복 사용


[8. 스프링부트 Log4j2 적용하기](https://linked2ev.github.io/gitlog/2019/08/26/springboot-mvc-8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-Log4j2-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0/)
+ SLF4J 설명
+ Log4j2 적용 및 패턴 설정


[9. 스프링부트 Log4jdbc 쿼리 로그 설정](https://linked2ev.github.io/gitlog/2019/08/27/springboot-mvc-9-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-Log4jdbc-%EC%BF%BC%EB%A6%AC-%EB%A1%9C%EA%B7%B8-%EC%84%A4%EC%A0%95/)
+ 로거 옵션 설명


[10. 스프링부트 MVC - MyBatis DAO 방식](https://linked2ev.github.io/gitlog/2019/09/11/springboot-mvc-10-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-MyBatis-DAO-%EB%B0%A9%EC%8B%9D/)
+ @Repository 애너테이션을 사용해서 DAO 객체 생성


[11. 스프링부트 MVC - MyBatis 공통 DAO](https://linked2ev.github.io/gitlog/2019/09/11/springboot-mvc-11-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-MyBatis-%EA%B3%B5%ED%86%B5-DAO/)
+ 공통 DAO로 queryId와 parameter를 이용해 처리하는 방식


[12. 스프링부트 MVC - Interceptor 설정](https://linked2ev.github.io/gitlog/2019/09/15/springboot-mvc-12-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Interceptor-%EC%84%A4%EC%A0%95/)
+ Interceptor 설정 및 설명


[13. 스프링부트 MVC - Filter 설정](https://linked2ev.github.io/gitlog/2019/09/15/springboot-mvc-13-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Filter-%EC%84%A4%EC%A0%95/)
+ Filter 설정 및 설명


[14. 스프링부트 MVC - Spring AOP 설정](https://linked2ev.github.io/gitlog/2019/09/22/springboot-mvc-14-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Spring-AOP-%EC%84%A4%EC%A0%95/)
+ AOP 간략 설명 및 설정


[15. 스프링부트 MVC - Transaction 설정... 작성중..?](#)
+ 작성중..?