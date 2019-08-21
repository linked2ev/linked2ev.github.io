---
layout: post
title:  "[MySQL/MariaDB] - Loading class com.mysql.jdbc.Driver'. This is deprecated. The new driver class iscom.mysql.cj.jdbc.Driver'."
subtitle:   "[DB]"
categories: database
tags: dbtool-sub
comments: true
---

Loading class com.mysql.jdbc.Driver'. This is deprecated. The new driver class iscom.mysql.cj.jdbc.Driver'.

<br>

## 이슈
---

> Loading class com.mysql.jdbc.Driver'. This is deprecated. The new driver class iscom.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.

<br>

## 해결
---

```
spring.datasource.hikari.driver-class-name=com.mysql.jdbc.Driver
```

```
spring.datasource.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
```

아래 설정으로 `com.mysql.cj.jdbc.Driver` 으로 수정해준다.

<br>


---
[참고]
- https://stackoverflow.com/questions/52032739/loading-class-com-mysql-jdbc-driver-this-is-deprecated-the-new-driver-class