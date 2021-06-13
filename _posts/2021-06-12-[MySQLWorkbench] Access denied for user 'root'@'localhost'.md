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

MySQLWorkbench 에서 DB 접속에 root 계정, 일반계정으로 mysql 접속 실패 오류

```
Failed to Connect to MySQL at localhost:3306 with user root
Access denied for user 'root'@'localhost'
```

<br>

[![mariadb_workbench_01](/assets/img/2021/mariadb_workbench_01.png)]()

<br><br>


# `원인`

계정에 대해 `비밀번호 적용 방식`과 `비밀번호 할당`시 오류이다. mysql.user가 뷰테이블로  MariaDB 10.5 이상부터 변경 된거 같다.

<br><br>


# `해결`

> 해결 방법

<br>

[해결 방법](https://linked2ev.github.io/database/2021/06/12/MariaDB-ERROR-1698-(28000)-Access-denied-for-user-'root'@'localhost'/)

<br>

> 정상확인

[![mariadb_workbench_02](/assets/img/2021/mariadb_workbench_02.png)]()

<br><br>


---
