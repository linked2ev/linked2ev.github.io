---
layout: post
title:  "[MacBook] 맥북 tomcat 설치"
subtitle:   "[MacBook]"
categories: devsub
tags: macbook
comments: true
---

맥북에 tomcat 설치... 스프링부트 쓰다가 스프링 웹 프로젝트가 필요하니 tomcat 이 필요

<br><br> 


# homebrew 로 tomcat 설치 진행

## 1. tomcat 버젼 확인

```
brew search tomcat
```

<br>

[![install_tomcat_for_mac_01](/assets/img/2021/install_tomcat_for_mac_01.png)]()

<br><br>


## 2. tomcat 설치

tomcat 버젼이 나온다. tomcat9로 설치를 진행 해준다.

```
brew install tomcat@9
```

<br>

[![install_tomcat_for_mac_02](/assets/img/2021/install_tomcat_for_mac_02.png)]()

<br><br>



## 3. hombrew tomcat 설치 확인

```
brew list
```

<br><br>


## 4. Mac에서 tomcat 설치 파일 경로 확인

- Finder에서 단축키 `command + shift + G`
- '/usr/local/Cellar/' 로 이동해보면 tomcat 폴더도 생성 확인

<br><br>


## 5. tomcat 실행

```
linked2ev@linked2ev-MacBook-Pro / % 
linked2ev@linked2ev-MacBook-Pro / % cd /usr/local/Cellar/tomcat/10.0.5/bin
linked2ev@linked2ev-MacBook-Pro bin % ./catalina start
Using CATALINA_BASE:   /usr/local/Cellar/tomcat/10.0.5/libexec
Using CATALINA_HOME:   /usr/local/Cellar/tomcat/10.0.5/libexec
Using CATALINA_TMPDIR: /usr/local/Cellar/tomcat/10.0.5/libexec/temp
Using JRE_HOME:        /usr/local/opt/openjdk
Using CLASSPATH:       /usr/local/Cellar/tomcat/10.0.5/libexec/bin/bootstrap.jar:/usr/local/Cellar/tomcat/10.0.5/libexec/bin/tomcat-juli.jar
Using CATALINA_OPTS:   
Tomcat started.
linked2ev@linked2ev-MacBook-Pro bin % 
```

<br>

[![install_tomcat_for_mac_03](/assets/img/2021/install_tomcat_for_mac_03.png)]()

<br>

> tomcat 실행/중지

```
linked2ev@linked2ev-MacBook-Pro bin % ./catalina start
linked2ev@linked2ev-MacBook-Pro bin % ./catalina stop
```


<br><br>


## 6. tomcat 실행 확인

- 주소창에 'localhost:8080' 으로 접속 후에 톰캣 고양이가 나오면 된다.

<br>

[![install_tomcat_for_mac_04](/assets/img/2021/install_tomcat_for_mac_04.png)]()

