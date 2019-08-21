---
layout: post
title:  "[MySQL/MariaDB] - java.sql.SQLException: The server time zone value"
subtitle:   "[DB]"
categories: database
tags: dbtool-sub
comments: true
---

java.sql.SQLException: The server time zone value 

<br>

## 이슈
---

- 스프링 웹 프로젝트에서 MySQL/MariaDB 서버에 접속 시 한글문제로 timezone 관련 에러

> java.sql.SQLException: The server time zone value '´???¹?±¹ ???Ø½?' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.<br><br>
Caused by: com.mysql.cj.exceptions.InvalidConnectionAttributeException: The server time zone value '´???¹?±¹ ???Ø½?' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.

<br>

## 해결
---

## 1. 웹 프로젝트 내 스프링 설정 방법
---

- DB 연결 URL parameter 추가: `serverTimezone=UTC`

```
spring.datasource.hikari.jdbc-url=jdbc:mysql://localhost:3307/test?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
```

<br>


## 2. MariaDB 서버 내 설정 방법
---

1. MySQL/MariaDB 설치 경로 이동  
2. `default-time-zone = "+9:00"` 설정 추가
    + > 설치경로/data/my.ini
    
        ```ini
        [mysqld]
        default-time-zone = "+9:00"
        ```
    + (default-time-zone = "Asia/Seoul" 해당 설정 해결안됨)  
3. MySQL/MariaDB 재시작


<br>


---
[참고]
- https://offbyone.tistory.com/318