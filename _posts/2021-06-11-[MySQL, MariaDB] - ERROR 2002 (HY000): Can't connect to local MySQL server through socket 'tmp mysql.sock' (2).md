---
layout: post
title:  "[MySQL/MariaDB] - ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)"
subtitle:   "[DB]"
categories: database
tags: dbtool-sub
comments: true
---

ERROR 2002 (HY000) : Can’t connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)  
오류에 대한 포스팅이다.

<br><br>


# `에러`

```
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
```

또는

```
ERROR 2002 (HY000) : Can’t connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
```

<br>

# `원인`

## `1) 원인`

mysql.sock 파일이 경로 및 심볼릭링크 정확하지 않아서 발생하는 오류

<br>


## `2) 원인`

mysql.sock 파일에 대한 권한 문제 오류

<br>


## `3) 원인`

실질적인 설정 문제 일 수도 있으나, 설정 없이 갑자기 안되는 경우에는 Mac 업데이트로 인한 my.cnf 손상이나 mysql 실행 도중 손상되서 mysql.sock 문제가 발생 한 이유이다.

또는 구글링을 해서 여러 mysql 설정이 기술 된 my.cnf파일을 mysql, mariadb 또 버전이나 OS 환경을 고려하지 않고 무작정 가져다 사용하면 에러 발생가 발생 할 수 있다.

이럴 경우에는 data 백업 한 후에 재 설치해야 한다.


<br><br>


# `해결`

## `1) 해결`
---

다른 오류들은 해결하거나 없어야 한다.

## 1. mysql 실행

<br>

## 2. mysql.sock 위치 찾기

```
$ mysql_config --socket
```

<br>

## 3. my.cnf 설정된 socket 경로를 심볼릭링크 설정

<br>

> $ `sudo ln -s /var/lib/mysql/mysql.sock /tmp/mysql.sock`

```
$ sudo ln -s /var/lib/mysql/mysql.sock /tmp/mysql.sock
```

> mysql.sock 심볼릭링크 확인

```
$ ls -al
lrwxr-xr-x  1 linked2ev  admin   25  6 20 16:16 mysql.sock -> /var/lib/mysql/mysql.sock
```

`/tmp/mysql.sock 라는 원본파일이 /var/lib/mysql/mysql.sock 에 Symbolic Link가 연결되었다는 의미`

<br><br>


## `2) 해결`
---

<br>

> 디렉토리에 권한 변경

```
$ sudo chmod -R 777 /var/lib/mysql
```

<br>

> 파일 소유자/소유자 그룹 변경 

$ chown <사용자>:<사용자그룹> <파일>

```
$ sudo chown root:admin /var/lib/mysql
```

<br><br>


## `3) 해결`
---

구글링을 해서 여러 mysql 설정이 기술 된 my.cnf파일을 mysql, mariadb 또 버전이나 OS 환경을 고려하지 않고 무작정 가져다 사용시 에러 발생가 발생 할 수 있다. 

설정파일을 원복하거나 재설치하면 된다.

<br>

[참고] -> [[Homebrew] 3. MariaDB 설치 for Mac](https://linked2ev.github.io/database/2021/04/15/MariaDB-3.-MariaDB-%EC%84%A4%EC%B9%98-for-Mac/)

<br><br>


---

