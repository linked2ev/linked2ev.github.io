---
layout: post
title:  "3. Springboot Gradle 프로젝트 생성"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

Springboot Gradle 프로젝트 생성

<br>

---

> 개발 환경  

- OS: Window 10
- Dev Tool: Eclipse IDE 2019-06
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.7
- Build Tool: Gradle v3

---

<br>
  

# 1. Springboot 프로젝트 생성

## Springboot 프로젝트 생성
---

[![sts-mvc-3-01](/assets/img/devlog/201908/sts-mvc-3-01.png)]()

[![sts-mvc-3-02](/assets/img/devlog/201908/sts-mvc-3-02.png)]()

[![sts-mvc-3-03](/assets/img/devlog/201908/sts-mvc-3-03.png)]()

<br>

## Springboot 의존성 추가

[![sts-mvc-3-04](/assets/img/devlog/201908/sts-mvc-3-04.png)]()

- Developer Tools
    + Spring Boot DevTools
    + Lombok
- Template Engines
    + Thymeleaf
- Web
    + Spring Web Starter

<br>

- 이와 같이 스프링부트는 의존성을 추가해서 프로젝트를 생성하면 Maven이나 Gradle 빌드 툴을 이용해서 해당 모듈에 필요한 하위 라이브러리까지 다운로드한다.
- 이렇게 스프링부트 프로젝트는 생성되었다.

<br><br>


# 2. Build Path 설정

- 생성 된 프로젝트에 우클릭 > Build Path > Configure Build Path 클릭
- Libraries Tab 클릭
- JRE System Library 선택 > Edit
- Workspace defualt JRE 클릭

[![sts-mvc-3-05](/assets/img/devlog/201908/sts-mvc-3-05.png)]()

<br>


- Order and Export Tab 클릭
- 순서 변경

[![sts-mvc-3-06](/assets/img/devlog/201908/sts-mvc-3-06.png)]()

<br>

- 이와 같은 작업이 끝나며 다시 build를 하고 아래와 같이 Springboot + gradle 프로젝트 구조로 되어 있을 것이다.

[![sts-mvc-3-07](/assets/img/devlog/201908/sts-mvc-3-07.png)]()

<br>


# Hello World 출력하기

- 우선 스프링부트 프로젝트가 잘 되는지 Hello World를 찍어보기 위해 @RestController를 선언하고 확인해보자.
- NeoAdminApplication.java(or ~~Application.java)에 소스를 아래와 같이 수정한다
- 프로젝트 우클릭 > Run As > Spring boot App 클릭해서 서버를 실행한 후 localhost:8080에 접속하면 "Springboot Hello World"가 찍히는 것을 확인 할 수 있다.

<br>


> NeoAdminApplication.java

```java
package neo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@SpringBootApplication
public class NeoAdminApplication {

	@RequestMapping("/")
	public String index () {
		return "Springboot Hello World";
	}
	
	public static void main(String[] args) {
		SpringApplication.run(NeoAdminApplication.class, args);
	}
}

```

<br>

---
참고