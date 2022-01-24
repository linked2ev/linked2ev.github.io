---
layout: post
title:  "[MySQL] Loading class 'com.mysql.jdbc.Driver'. This is deprecated. The new driver class is 'com.mysql.cj.jdbc.Driver'."
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

springboot yml 설정 시에 Error: Loading class `com.mysql.jdbc.Driver'. This is deprecated.

<br><br>


# 원인

Loading class `com.mysql.jdbc.Driver'. This is deprecated.

<br><br>


# 오류

```
Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.
```

<br><br>

# 해결

spring boot 2 이상 버전과 mysql-connector-java 6이상의 버전 문제로  
JDBC 연결할 때 드라이버 정보를 com.mysql.jdbc.Driver에서 `com.mysql.cj.jdbc.Driver`로 바꿔주면 된다.

<br>

> log4jdbc properties에 추가

```properties
log4jdbc.auto.load.popular.drivers=false
log4jdbc.drivers=com.mysql.cj.jdbc.Driver
```

<br><br>


---
[참고]
- https://ildann.tistory.com/12