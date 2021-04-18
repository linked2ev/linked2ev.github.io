---
layout: post
title:  "[IntelliJ] JSP 기본 객체 Cannot resolve method ~ (java) 에러"
subtitle:   "[IntelliJ]"
categories: devsub
tags: intellij
comments: true
---

JSP 객체 Cannot resolve method 'method(java.lang.String)' 에러 발생 시에  
tomcat 에서 '`servlet-api.jar`, `jsp-api.jar`' 라이브러리 추가 설정

이클립스에서 프로젝트 > Build Path > Configure Build Path..를 통해 설정하는 기능과 비슷한 개념이다.

<br><br>




# JSP 객체 Cannot resolve method 'method(java.lang.String)'

[![jsp-servlet-jar-00](/assets/img/2021/jsp-servlet-jar-00.png)]()

<br><br>

위와 같이 jsp 파일에서 외부라이브러리가 없어서 오류가 발생한다. Spring Web 프로젝트에서 spring-mvc 의존성에  
servlet-api.jar, jsp-api.jar 가 포함되지 않기 때문에 발생하는 원인이다.

<br>

일반적으로 프로젝트 진행시에는 `maven이나 gradle 빌드 도구들로 의존성을 주거나` 또는 `필요시에 .jar 파일들을 외부라이브러리 형태로 추가`한다. 

이클립스에서 프로젝트 > Build Path > Configure Build Path..를 통해 설정하는 기능과 비슷한 개념이다.



# 1. `servlet-api.jar` 파일 추가

servlet-api.jar 파일은 tomcat에서 servlet 컴파일하는 역할을 하는 모듈이다.  
Servlet API 스펙의 인터페이스 및 클래스를 포함하는 라이브러리입니다.

- [참고 :  Servlet이란](https://linked2ev.github.io/devlog/2018/10/17/JSP-2.-What-Servlet/)

<br>


## 외부라이브러리 방식으로 .jar 파일 추가

- `File > Project Structure` or `command + ;`

<br>

[![jsp-servlet-jar-01](/assets/img/2021/jsp-servlet-jar-01.png)]()<br>

[![jsp-servlet-jar-02](/assets/img/2021/jsp-servlet-jar-02.png)]()<br>

<br>

> `command + shift + .` 숨겨진 폴더 보기

<br>

[![jsp-servlet-jar-03](/assets/img/2021/jsp-servlet-jar-03.png)]()<br>

[![jsp-servlet-jar-04](/assets/img/2021/jsp-servlet-jar-04.png)]()<br>

<br><br>


# 2. `jsp-api.jar` 파일 추가

jsp 라는 것은 자바에서 웹 서비스를 제공하기 위해 만든 규약이며, jsp-api.jar 파일은 .jsp 파일들은 .java 파일로 컴파일 해주는 개념이다.

- [참고 :  JSP 란?](https://linked2ev.github.io/devlog/2018/10/17/JSP-1.-What-JSP/)

<br>

## 외부라이브러리 방식으로 .jar 파일 추가

[![jsp-jspapi-jar-00](/assets/img/2021/jsp-jspapi-jar-00.png)]()<br>

[![jsp-jspapi-jar-01](/assets/img/2021/jsp-jspapi-jar-01.png)]()<br>

<br><br>



