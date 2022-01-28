---
layout: post
title:  "1. Spring Cloud Config Server"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-cloudconfig
comments: true
---

[springboot yaml 파일로 변경](https://linked2ev.github.io/gitlog/2022/01/24/8.-springboot-yaml-파일로-변경/) 포스팅에 이어서 Spring Cloud Config 설정에 대한 포스팅이다.

<br><br>


# 1. Spring Cloud Config란?

분산된 환경에서 설정 파일을 외부로 분리해서 Config Server를 통해 전달하는 구조이다. 개발/테스트/운영 환경에서 모든 환경 구성을 간편하게 관리할 수 있다.

MSA 구조에서 수 많은 독립 된 서비스들을 설정파일을 한 곳에서 중앙집중관리를 통해 관리가 용이하다.

<br><br>


# 2. 설정 파일 외부 처리(Cloud Config)

기존 서비스의 설정파일(application.yml)을 외부(gitlab, github, local 등)로 분리 시키자. 설정 파일 내용을 아래처럼 수정 후에 yml을 application-local, application-dev, application-prod 각각 생성

<br>

> application-{profile}

```yml
server:
  port: 8080

spring:
  #profiles: local, dev, prod
  config:
    activate:
      on-profile: dev # local, dev, prod

  datasource:
    hikari:
      connection-test-query: SELECT 1
      allow-pool-suspesion: true
      driver-class-name: net.sf.log4jdbc.sql.jdbcapi.DriverSpy
      jdbc-url: jdbc:log4jdbc:mysql://localhost:3306/demo_neo?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
      username: root
      password: 1111

  thymeleaf:
    cache: false
    
  resources:
    cache:
      period: 0

  jpa:
    database: mysql
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
    generate-ddl: true
    hibernate:
      use-new-id-generator-mappings: false

mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

<br><br>


# 3. Github Repository 생성

github(외부)에 본 포스팅에서는 "neo-spring-cloud-config"명으로 repository 생성 후에 
생성 된 외부 설정 파일을 github에서 관리한다.

[![spring-cloud-config-repo-s1](/assets/img/2022/spring-cloud-config-repo-s1.png)]() <br>


<br><br>


다음은 설정파일을 불러와서 배포하는 역할을 하는 Config Server에 대한 포스팅이다.