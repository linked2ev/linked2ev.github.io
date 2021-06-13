---
layout: post
title:  "[MySQLWorkbench] Access denied for user 'root'@'localhost'"
subtitle:   "[DB]"
categories: database
tags: dbtool-sub
comments: true
---

맥북에 MariaDB 설치 다시 설치하다 생긴 이슈이다. MariaDB 10.5 이상부터 변경 된거 같다. MySQLWorkbench 로 접속시 에러 이슈

<br><br>


# `에러`

MySQLWorkbench 에서 외부접속 및 IP주소(127.0.0.1) 접속시에 오류 메시지

<br>

[![mariadb_grant_ip_01](/assets/img/2021/mariadb_grant_ip_01.png)]()

[![mariadb_grant_ip_02](/assets/img/2021/mariadb_grant_ip_02.png)]()

<br>

> Alert

---
Your connection attempt failed for user 'root' to the MySQL server at 127.0.0.1:3306:Unknown database 'DbName'

Please:  
1 Check that MySQL is running on address 127.0.0.1   
2 Check that MySQL is reachable on port 3306 (note: 3306 is the default, but this can be changed)  
3 Check the user root has rights to connect to 127.0.0.1 from your address (...

---

<br><br>


# `원인`

외부 IP 로컬에 외부접속 허용되지 않아서 발생함

<br><br>


# `해결`

> 해결 방법

<br>

[해결 방법: IP 외부접속 허용](https://linked2ev.github.io/database/2021/06/13/MariaDB-4.-%EC%99%B8%EB%B6%80-IP-%EB%A1%9C%EC%BB%AC%EC%97%90-%EC%99%B8%EB%B6%80%EC%A0%91%EC%86%8D(127.0.0.1)-%ED%97%88%EC%9A%A9-for-Mac/)

<br>

> 정상확인

[![mariadb_grant_ip_02-1](/assets/img/2021/mariadb_grant_ip_02-1.png)]()

<br><br>


---
