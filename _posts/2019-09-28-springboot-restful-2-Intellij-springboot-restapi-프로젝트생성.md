---
layout: post
title:  "2. springboot restapi 프로젝트생성"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-restful
comments: true
---

`Intellij`로 `restful`하게 스프링부트 restapi 프로젝트를 개발해보자. 스프링부트에 대해서는 알고 있다는 전제로 작성~

<br><br>


> 개발 환경 및 내용

- OS: Window 10
- Dev Tool: Intellij - 2019.2.3
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.8
- Build Tool: Gradle v3
- RDBMS: MariaDB

---

> 참고 URL

---


<br>

# Intellij로 스프링부트 프로젝트 생성

> 상단 메뉴 > File > New > Project

[![intellj-project-create-s1](/assets/img/devlog/201909/intellj-project-create-s1.png)]()


[![intellj-project-create-s2](/assets/img/devlog/201909/intellj-project-create-s2.png)]()


[![intellj-project-create-s3](/assets/img/devlog/201909/intellj-project-create-s3.png)]()


[![intellj-project-create-s4](/assets/img/devlog/201909/intellj-project-create-s4.png)]()

<br><br>


NeoApplication.java에서 아래와 같이 mybatis dependency를 추가했지만 datasource를 설정안했기에 추가한다.

그리고 프로젝트를 실행하면 된다.

```java
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
```


> NeoApplication.java

```java
@SpringBootApplication
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
@RestController
public class NeoApplication {

    public static void main(String[] args) {
        SpringApplication.run(NeoApplication.class, args);
    }

    @GetMapping("/hello")
    public String hello() {
        return "Hello";
    }
}
```


[![intellj-project-create-s5](/assets/img/devlog/201909/intellj-project-create-s5.png)]()



<br><br>

---
[참고]