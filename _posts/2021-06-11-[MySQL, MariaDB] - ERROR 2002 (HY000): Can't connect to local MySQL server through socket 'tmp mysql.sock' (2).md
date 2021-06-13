---
layout: post
title:  "[MySQL/MariaDB] - ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)"
subtitle:   "[DB]"
categories: database
tags: dbtool-sub
comments: true
---

갑자기 이유없이 에러가 난 상황이다. 시간만.. 날렸다.

<br><br>

# `에러`

```
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
```

<br>

# `원인`

실질적인 설정 문제 일 수도 있으나, 설정 없이 갑자기 안되는 경우에는 Mac 업데이트로 인한 my.cnf 손상이나 mysql 실행 도중 손상되서 mysql.sock 문제가 발생 한 이유이다.

이럴 경우에는 data 백업 한 후에 재 설치해야 한다.

<br>

# `해결`

# MySQL/MariaDB 삭제

> mysql 서비스 중지

```
brew services stop mysql
or
brew services stop mariadb
```

<br>

> mysql 삭제

```
brew unlink mysql
brew remove mysql
brew uninstall mysql
or
brew unlink mariadb
brew remove mariadb
brew uninstall mariadb
brew cleanup
```

<br>

# 맥에서 mysql/mariadb 완전 삭제

> homebrew 로 MySQL을 설치한 경우

```
sudo rm -rf /usr/local/var/mysql
sudo rm -rf /usr/local/bin/mysql*
sudo rm -rf /usr/local/Cellar/mysql
```

<br><br>

# 1. Homebrew MariaDB 설치

```
brew update
brew install mariadb
```

# 2. MariaDB 시작

```
mysql.server start
```

<br><br>


> MariaDB Server 자동 시작, Homebrew 서비스 기능

```
brew services start mariadb
```

<br><br>


# 3. MariaDB 접속

```
mysql -u root -p
```


<br>

