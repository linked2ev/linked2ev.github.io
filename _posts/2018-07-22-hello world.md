---
layout: post
title:  "[Springboot] Hello World 로컬에서 출력하기"
subtitle:   "[Springboot]"
categories: gitlog
tags: bmw
comments: true
---

이번 포스트에서는 로컬에서 Hello World를 출력해보겠습니다.


## 3. DemoApplication 설명

우선 스프링부트를 실행하면 본 프로젝트에서는 에러가 발생한다.  
왜냐하면 아직 application.properties에 mysql에 대한 설정 값이 없기 때문이다.  

```java
package com.bmw.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;

@SpringBootApplication
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class BmwApplication {

	public static void main(String[] args) {
		SpringApplication.run(BmwApplication.class, args);
	}
}
```

3-1. BmwApplication 클래스  
위에 BmwApplication.java 클래스에 대해서 간단히 설명하면 
스프링부트를 단독실행 및 스크립트를 통한 실행을 지원하기 위한 필수 클래스라고 생각하면 된다.

3-2. @SpringBootApplication  
@Configuration + @EnableAutoConfiguration + @ComponentScan 의 역할을 한다.
+ @Configuration  
ㄴ해당 Java class가 Spring 환경 설정에 관련 된 파일이라는 것을 알려준다.
+ @EnableAutoConfiguration   
ㄴclasspath의 내용에 기반해서 SpringBoot 설정 작업들을 자동화해준다.
+ @ComponentScan  
ㄴ스프링 설정에서의 <context:component-scan base-package="com.example">와 같이  
  하위 패키지들을 스캔하여 Bean을 찾고 등록하는 역할이다.

3-3. @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})  
우선 임시로 mysql이 추가되어 있기에 DataSource자동 설정을 제외한다.

<br><br>

## 4. HomeController.java 생성

HomeController.java 생성 후 아래와 같이 코딩을 작성한다.

```java
package com.bmw.demo;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HomeController {

	@RequestMapping("/home")
	public String home() {
		return "Hello, Spring boot!";
	}
	
}
```

4-1. @RestController
@RestController의 주석은 RESTful 웹 서비스의 생성을 단순화하기 위해 스프링 4.0에서부터 도입되었고 @Controller + @ResponseBody 를 결합한 역할을 한다.  

여기서 잠깐, SOAP와 RESTful의 차이의 비슷한 관점처럼 @Controller는 view에 초점을 두고,
@RestController는 data에 초점을 둔다고 생각 할 수 있다.  
(추후 이와 관련 된 포스트는 따로 작성예정)

<br>

[![Hello World STEP1](/assets/img/bmw/201807/2018-07-15-HelloPrintStep1.png)]()  
  
<br><br>

## 5. Hello World 출력

스프링부트에서는 config설정들을 application.properties 파일에서 관리하는데  
포트를 설정해준다.(기본은 80이다.)

```xml
server.port = 8070
```
<br>

프로젝트 우클릭 > Run AS > Spring Boot App 실행

[![Hello World STEP2](/assets/img/bmw/201807/2018-07-15-HelloPrintStep2.png)]()  

<br>

서버를 구동하면 콘솔창에 예쁘게 스프링부트가 찍힌다.

[![Hello World STEP3](/assets/img/bmw/201807/2018-07-15-HelloPrintStep3.png)]()  

<br> 

이제 브라우저에 localhost:8070/home 에 접속하면  
아래와 같이 문구가 출력된다.

[![Hello World STEP4](/assets/img/bmw/201807/2018-07-15-HelloPrintStep4.png)]()

그럼 이만~
<br><br> 

---
스프링부트에 대해 이미 고견이 있는 고수님들의 블로그들이 많기에
해당 포스트에서는 개인프로젝트 개발 진행과정에 초점을 두겠습니다.