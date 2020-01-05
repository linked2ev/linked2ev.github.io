---
layout: post
title: "6. springboot profile(프로파일) 설명"
subtitle: "[springboot]"
categories: gitlog
tags: springboot-restful
comments: true
---

스프링 웹 프로젝트로 개발하면 로컬, 개발서버, 테스트서버, 운영서버 배포한경에 따라 프로파일 활용에 대한 포스팅이다.

<br><br>


> 개발 환경 및 내용

- OS: Window 10
- Dev Tool: Intellij - 2019.2.3
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.8
- Build Tool: Gradle v3
- RDBMS: MariaDB
- #Lombok #Swagger-UI

---

> 관련 URL

---

<br><br>

## 스프링 프로파일
---

스프링 웹 프로젝트로 개발하면 로컬, 개발서버, 테스트서버, 운영서버 등 datasource, 폴더 경로, 외부 테스트 모듈 경로 등 배포환경에 따라 설정 값이 다르다. 그래서 배포 할 때 설정 값을 다르게 해야하는데, 번거롭지 않게 파일로 관리해 배포환경에 따라 설정 값들을 적용하게끔 도와주는 스프링 프로파일이라는 기능을 지원해준다.

예를 들어 스프링 프로젝트에서 Maven설정이나, 빌드 스크립트로 스프링 프로파일 경로를 지정해 application.properties 등 환경설정 파일들을 배포환경에 따라 해당 경로의 프로파일들을 읽어들어서 설정 값들을 적용한다.

```
profile
  ㄴ local
    ㄴ application.properties
    ㄴ config.xml
    ㄴ ...
  ㄴ dev
    ㄴ application.properties
    ㄴ config.xml
    ㄴ ...
  ㄴ prod
    ㄴ application.properties
    ㄴ config.xml
    ㄴ ...
```

<br><br>


## 스프링부트에서 스프링프로파일 적용
---

## 1.  배포환경에 따라 .properties 파일 생성

스프링부트에서도 스프링프로파일 기능으로 간단하게 .properties 파일들로 배포환경 설정을 관리 할 수 있다. 아래와 같이 해당 배포환경에 따라 .properties을 만들어주고 

```
application-{Profile ID}.properties
```

```gradle
application.properties       //프로젝트 공통 설정 파일
application-local.properties //로컬 설정 파일
application-dev.properties   //개발 설정 파일
application-prod.properties  //운영 설정 파일
```

<br>


## 2. spring.profiles.active 설정

해당 파일에 spring.profiles.active 값을 설정해준다.

> application-dev.properties

```yaml
spring.profiles.active=dev

# hikari-mybatis-jdbc4jdbc 설정
spring.datasource.hikari.driver-class-name=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
spring.datasource.hikari.jdbc-url=jdbc:log4jdbc:mysql://localhost:3307/test?useUnicode=true&characterEncoding=utf-8
spring.datasource.hikari.username=root
spring.datasource.hikari.password=1111
```

<br>

Run Configuration에서 profile에 Profile ID를 입력("dev")하고 서버를 구동하면 spring.profiles.active에 적용 된 값의 application-{Profile ID}.properties 파일을 찾아서 배포한다.

<br>

> 다음 포스팅에서 이어서 .yaml 파일로 환경에 따라 프로파일을 적용진행
