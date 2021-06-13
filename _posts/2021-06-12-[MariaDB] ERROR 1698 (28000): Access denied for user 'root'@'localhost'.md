---
layout: post
title:  "[MariaDB] ERROR 1698 (28000): Access denied for user 'root'@'localhost'"
subtitle:   "[DB]"
categories: database
tags: dbtool-sub
comments: true
---

맥북에 MariaDB 설치 다시 설치하다 생긴 이슈이다. MariaDB 10.5 이상부터 변경 된거 같다. 해당 포스팅에는 MariaDB 10.4 이전 해결 방안도 작성.

<br><br>


# `에러`
 
root 계정, 일반계정으로 mysql 접속에 접속을 못한는 경우이다.

```
linked2ev@linked2ev-MacBook-Pro etc % mysql -uroot      
ERROR 1698 (28000): Access denied for user 'root'@'localhost'
```

<br>

또는 MariaDB 10.5 이상이면 아래와 같은 오류 발생시 

```
ERROR 1356 (HY000): View 'mysql.user' references invalid table(s) or column(s) or function(s) or definer/invoker of view lack rights to use them
```

<br><br>


# `원인`

계정에 대해 `비밀번호 적용 방식`과 `비밀번호 할당`시 오류이다. mysql.user가 뷰테이블로  MariaDB 10.5 이상부터 변경 된거 같다.

<br><br>


# `MariaDB 10.4 이하 해결(비밀번호 방식)`

<br>

[![mariadb_issue_03](/assets/img/2021/mariadb_issue_03.png)]()

<br>

아래와 같이 명령어로 해결 sudo 명령어로 mysql 를 접속한 후에 아래와 같이 비밀번호 방식을 바꾸면 된다.

<br>

```
$ sudo mysql -uroot
```
```
$ use mysql;
```
```
$ update user set plugin='mysql_native_password' where user='root';
```

```
MariaDB [(none)]> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> SELECT User, Host, Plugin FROM mysql.user;
+-------------+-----------------------+-----------------------+
| User        | Host                  | plugin                |
+-------------+-----------------------+-----------------------+
| mariadb.sys | localhost             | mysql_native_password |
| root        | localhost             | auth_socket           |
| linked2ev   | localhost             | mysql_native_password |
|             | localhost             |                       |
|             | linked2ev-macbook-pro |                       |
+-------------+-----------------------+-----------------------+
5 rows in set (0.003 sec)

MariaDB [mysql]> update user set plugin='mysql_native_password' where user='root';
```

<br>

다시 접속

```
$ mysql -uroot -p
```

<br><br>


# `MariaDB 10.5 이상 해결(비밀번호 할당)`

<br>

[![mariadb_issue_04](/assets/img/2021/mariadb_issue_04.png)]()

<br>

아래와 같이 에러 메시지가 발생 시에 MariaDB 10.5 이상부터 mysql.user 테이블은 뷰테이블로 아래와 같이 [Use SET PASSWORD](https://mariadb.com/kb/en/set-password/) 또는 [ALTER USER](https://mariadb.com/kb/en/alter-user/)방식으로 비밀번호를 할당해줘야 한다.

```
MariaDB [mysql]> update user set plugin='mysql_native_password' where user='root';
ERROR 1356 (HY000): View 'mysql.user' references invalid table(s) or column(s) or function(s) or definer/invoker of view lack rights to use them
```

<br>


[참고] - https://stackoverflow.com/questions/64841185/error-1356-hy000-view-mysql-user-references-invalid-tables-or-columns-o

<br><br>


아래와 같이 명령어로 해결 sudo 명령어로 mysql 를 접속한 후에 아래와 같이 비밀번호을 할당해준다.

```
$ sudo mysql -uroot
```
```
$ use mysql;
```

```
MariaDB [mysql]> set password for root@'localhost' = PASSWORD('1111');
Query OK, 0 rows affected (0.008 sec)
```
OR
```
MariaDB [mysql]> ALTER USER root@localhost IDENTIFIED BY '1111';
Query OK, 0 rows affected (0.009 sec)
```

<br>

다시 mysql 접속을 시도해보면 "$ mysql -uroot" 비밀번호를 물어보며 "mysql -uroot -p" 로 정상적으로 로그인을 하는걸 볼 수 있다.

```
linked2ev@linked2ev-MacBook-Pro etc % mysql -uroot     
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
linked2ev@linked2ev-MacBook-Pro etc % mysql -uroot -p
Enter password: 
```

<br>

[![mariadb_issue_05](/assets/img/2021/mariadb_issue_05.png)]()

<br><br><br>

---

<br>


## Use SET PASSWORD

SET PASSWORD 문은 기존 MariaDB 사용자 계정에 암호를 할당한다.

<br>

> 구문

```mysql
SET PASSWORD [FOR user] =
{
    PASSWORD('some password')
    | OLD_PASSWORD('some password')
    | 'encrypted password'
}
```

<br>

> 예제

```
SET PASSWORD FOR 'bob'@localhost = PASSWORD('1111');
```

<br>

[![mariadb_issue_01](/assets/img/2021/mariadb_issue_01.png)]()


<br><br>


## ALTER USER .. IDENTIFIED BY

IDENTIFIED BY절을 사용하여 계정에 암호를 할당한다. 암호는 일반 텍스트로 지정.

<br>

> 구문

```
ALTER USER [IF EXISTS] 
user_specification [,user_specification] ...
  [REQUIRE {NONE | tls_option [[AND] tls_option] ...}]
  [WITH resource_option [resource_option] ...]
  [lock_option] [password_option] 
```

<br>

> 예제

```
ALTER USER foo2@test IDENTIFIED BY '1111';
```

<br>

[![mariadb_issue_02](/assets/img/2021/mariadb_issue_02.png)]()


<br><br>

---

- https://stackoverflow.com/questions/64841185/error-1356-hy000-view-mysql-user-references-invalid-tables-or-columns-o
- https://mariadb.com/kb/en/set-password/
- https://mariadb.com/kb/en/alter-user/