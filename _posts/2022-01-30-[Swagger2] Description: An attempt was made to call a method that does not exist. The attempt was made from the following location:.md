---
layout: post
title:  "[Swagger2] Description: An attempt was made to call a method that does not exist. The attempt was made from the following location:"
subtitle: "[Java]"
categories: spring
tags: spring-devhistory
comments: true
---

Springboot + HATEOAS 프로젝트에서 Springboot, Gradle, Swagger 버전 변경 시에 에러 발생으로 인한 에러로 아래 에러에 대한 궁극적인 해결방법이 아닐 수가 있음.

<br><br>


# 에러

```
*************************** APPLICATION FAILED TO START ***************************

    Description:

    An attempt was made to call a method that does not exist. The
    attempt was made from the following location:

        org.springframework.data.rest.core.support.UnwrappingRepositoryInvokerFactory.<init>(UnwrappingRepositoryInvokerFactory.java:57)

    The following method did not exist:

        'org.springframework.plugin.core.PluginRegistry org.springframework.plugin.core.PluginRegistry.of(java.util.List)'

    The method's class, org.springframework.plugin.core.PluginRegistry,
    is available from the following locations:

        jar:file:/home/md7zn4/.m2/repository/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar!/org/springframework/plugin/core/PluginRegistry.class

    It was loaded from the following location:

        file:/home/md7zn4/.m2/repository/org/springframework/plugin/spring-plugin-core/1.2.0.RELEASE/spring-plugin-core-1.2.0.RELEASE.jar


    Action:

    Correct the classpath of your application so that it contains a
    single, compatible version of
    org.springframework.plugin.core.PluginRegistry
```

<br><br>


# Fixed Version

Springboot + HATEOAS 프로젝트에서 스프링부트 버전업에 따라 그레들, 스웨거도 
Swagger3으로 생각없이 버전업에 따라 에러 발생한 것으로 아래와 같이 버전 맞춤  
Swagger2 시에 404 not found error 시에도 아래와 같이 해결이 될 수 있음.

본인 프로젝트가 Springboot 인지 Spring 프로젝트 기반인지 WebMvc 등 의존성 주입에 따라 해결 방법이 상이하므로 [해당 포스팅 참고](https://www.baeldung.com/swagger-2-documentation-for-spring-rest-api)해서 먼저 확인하고 에러 해결.

<br>

> 본 포스팅 개발 구성 환경 Fixed Version

- Springboot : 2.5.9
- gradle : 6.9
- Swagger2 : 2.9.2

<br>

> build.gradle 추가

```
implementation 'org.springframework.plugin:spring-plugin-core:1.2.0.RELEASE'
```

<br><br>


---
