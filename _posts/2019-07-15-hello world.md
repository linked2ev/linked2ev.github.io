---
layout: post
title:  "Hello World 로컬에서 출력하기"
subtitle:   "[블로그]"
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
위에 BmwApplication.java 클래스에 대해서 간단히 설명해 보면
스프링부트는 단독실행 및 스크립트를 통한 실행을 지원하기위해
스프링부트 개발환경에서 반드시 존재해야 하는 클래스라고 생각하면 된다.

3-2. @SpringBootApplication
@Configuration + @EnableAutoConfiguration + @ComponentScan 의 역할을 한다.
+ @Configuration  
: 해당 Java class가 Spring 환경 설정에 관련 된 파일이라는 것을 알려준다.
+ @EnableAutoConfiguration   
: classpath의 내용에 기반해서 SpringBoot 설정 작업들을 자동화해준다.
+ @ComponentScan  
: 스프링 설정에서의 <context:component-scan base-package="com.example">와 같이  
  하위 패키지들을 스캔하여 Bean을 찾고 등록하는 역할이다.

3-3. @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
우선 임시로 mysql이 추가되어 있기에 DataSource자동 설정을 제외한다.






## 4. HomeController.java 생성

HomeController.java 생성 후 아래와 같이 코딩을 작성한다.


## 3. 프로젝트 생성

이클립스와 거의 비슷하다.

우클릭 > New > Spring Starter Project
[![프로젝트 생성 STEP1](/assets/img/bmw/2018-07-15-createProjectStep1.png)]()

  
프로젝트명을 명시해주고 나는 빌드는 Gradle로 할 예정이다.  
Maven으로 해도 무방합니다.(추후 Gradle과 Maven에 대한 포스트는 작성 예정)
[![프로젝트 생성 STEP2](/assets/img/bmw/2018-07-15-createProjectStep2.png)]()

  
스프링부트는 프로젝트 생성 시 정말 심플하긴 하다.
자기가 필요한 것은 체크하고 진행하면 프로젝트가 세팅이 된다.
[![프로젝트 생성 STEP3](/assets/img/bmw/2018-07-15-createProjectStep3.png)]()


---
스프링부트에 대해 이미 고견이 있는 고수님들의 블로그들이 많기에
해당 포스트에서는 개인프로젝트 개발 진행과정에 초점을 두겠습니다.