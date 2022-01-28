---
layout: post
title:  "2. Spring Cloud Config Server 생성"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-cloudconfig
comments: true
---

Cloud Config 설정파일을 불러와서 배포하는 역할을 하는 Spring Cloud Config Server 생성에 대한 포스팅이다.

<br><br>


# 1. Spring Cloud Config Server란?

Cloud Config 설정파일을 불러와서 배포하는 역할이다.  
Springboot에서 Cloud Config 모듈을 새로 만들자

<br><br>


# 2. Spring Cloud Config Server 생성

[![ccs-s1](/assets/img/2022/ccs-s1.png)]() <br>

[![ccs-s2](/assets/img/2022/ccs-s2.png)]() <br>

[![ccs-s3](/assets/img/2022/ccs-s3.png)]() <br>

[![ccs-s4](/assets/img/2022/ccs-s4.png)]() <br>

<br><br>


# 3. Config Server Application 설정

> ConfigServerApplication.java

해당 Springboot App이 Cloud Config Server 역할을 한다는 것을 명시 `@EnableConfigServer`

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }

}
```

<br><br>


> build.gradle

spring-cloud-config-server 의존성 주입

```js
plugins {
    id 'org.springframework.boot' version '2.6.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
    id 'war'
}

group = 'spring-cloud'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
    mavenCentral()
}

ext {
    set('springCloudVersion', "2021.0.0")
}

dependencies {
    implementation 'org.springframework.cloud:spring-cloud-config-server'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}
```

<br><br>


> application.yml

이전 포스팅에서 외부에 설정한 설정파일 경로를 설정한다.

```yml
server:
  port: 8888

spring:
  cloud:
    config:
      server:
        git:
          default-label: master
          uri: https://github.com/linked2ev/neo-spring-cloud-config
          #username: private 경우
          #password: private 경우
```

