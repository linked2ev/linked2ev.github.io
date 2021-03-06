---
layout: post
title:  "[Springboot] 5. 스프링부트에서 View를 JSP사용"
subtitle:   "[Springboot]"
categories: gitlog
tags: springboot
comments: true
---

Springboot에서 스프링MVC처럼 JSP를 사용하기 위한 설정이다.


## 8. 스프링부트 View

요즘은 Front-End/Back-End 나눠서 Restful하게 데이터 관점으로 REST 웹 서비스 방식으로 많이도  있다. <del>해당 프로젝트의 프론트는 React로 할 예정이다.</del>

그래도 jsp를 주로 접하기 때문에 정리하고 갈 생각이다.  

우선 스프링부트에서 View는 아래처럼
> 리소스 구조

```
src
└─ main
   └─ resource
      └─ templates (View: thymeleaf, groovy, handlebars, velocity 등)
      └─ static    (css, js 등)
```

작업을 하면 알아서 [템플릿엔진](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-template-engines)들을 지원해준다.


## 9. 스프링부트에서 JSP

[스프링부트에서 jsp는 jar로 지원하지 않는다고 한다.](http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-developing-web-applications.html#boot-features-jsp-limitations)  

근데 그냥 스프링처럼 동일하게 프로젝트 구조를 맞추고 설정하면 된다고 한다.
(스프링부트가 내부톰캣을 사용하기도 하고, JSP가 Java Server Page라서 리소스 파일인 템플릿 엔진 형태만 공식적으로 지원하는 구조인거 같다.)

9-1. main 밑에 webapp/WEB-INF/views 폴더를 생성하고 index.jsp 작성
```
src
└─ main
   └─ webapp
      └─ WEB-INF
        └─ views
          └─ index.jsp
```
<br>

> index.jsp

```html
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	Springboot-web-mvc-jsp > index!
</body> 
</html>
```
<br>

9-2. 스프링 servlet 설정처럼 스프링부트에서도 설정

> 스프링 설정 dispatcher 파일

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="viewClass"  value="org.springframework.web.servlet.view.JstlView"/>
    <property name="prefix" value="/WEB-INF/jsp/" />
    <property name="suffix" value=".jsp" />
    <property name="order" value="1" />
</bean>
```
<br>

> 스프링부트 설정 pplication.properties 파일

```properties
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```
<br>

9-3. build.gradle 파일에 dependencies에 추가

```gradle
dependencies {
	compile('org.apache.tomcat.embed:tomcat-embed-jasper') // web-jsp
	compile('javax.servlet:jstl')                          // web-jstl  
}
```
<br>

9-4. gradle 빌드
프로젝트 우클릭 > Gradle > Refresh Dependencies  
<br>


9-5. localhost:8070 으로 확인해보자

[![springboot Web JSP STEP1](/assets/img/bmw/201807/2018-07-26-springbootWebJSPStep1.png)]() 
<br>


정상적으로 index.jsp를 바라보면 성공
<br><br>

--- 
다음 계획은 우선 JSP방식으로 스프링MVC 패턴을 진행하고, 다시 스프링부트에서 지원하는 템플릿형태로 새로 진행하겠습니다. 실제로 구현해봐야 재밌죠.