---
layout: post
title:  "8. 스프링부트 Log4j2 적용하기"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

우선 추후 개발하기 편하게끔 스프링부트에 Log4j2 부터 적용하겠습니다.

<br><br>


# 1. Logging이란?

프로그램을 개발 및 유지보수 그리고 에러 로그를 통해 어플리케이션 상태를 추적과 기록을 통해 모니터링이 가능하다. 

자바에서 Logging Framework로 java.util.logging, commons logging, log4j, logback 등 있으며 로그를 찍는 구현체이다. 이러한 구현체들을 추상화 시킨 것이 SLF4J이다.

<br><br>


# 2. SLF4J(Simple Logging Facade for Java)란?

SLF4J의 역할은 다양한 Logging Framework들의 facade(혹은 추상체) 역할을 함으로써
사용중인 Logging Framework를 변경해도 java 코드에 대한 변경이 필요 없다.

스프링 웹 프로젝트에서 log4j2 + slf4j 시 의존성 추가하고 개발 했을 것이다.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@RequestMapping("/board")
public class BoardController {
	
	private static final Logger logger = LoggerFactory.getLogger(BoardController.class);
```

`SLF4J 라는 추상화된 인터페이스로 로그 코드를 작성하고, 스프링 주입을 통해서 실제 구현체 Logging Framework에서 로그 코드를 구현한다.`


<br><br>


# 3. Log4j2 의존성 추가

자바 프로젝트에서 예전에 Logging Framework로 `Log4j`를 많이 사용했다. 그러다 개발이 중단되고 Log4j를 기반으로 성능이 크게 개선되고 기능 추가 된 `Logback`를 만들었다. 그 뒤에 Log4j에서 `Log4j2`로 보다 높은 성능으로 다시 릴리즈 시작되었다.

스프링부트에서 log4j2를 사용할려면 `spring-boot-starter-log4j2` 의 의존성을 추가하고 충돌을 방지하기 위해 `spring-boot-starter-logging` 를 제외시켜야 한다.

> build.gradle

```gradle
configurations {
    all {
        exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'
    }
}
```
```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-log4j2'
}
```

<br><br>


# 4. Log4j2.xml 설정 파일 추가

xml파일 설정 방법 외에 propeties 또는 json 파일로 설정이 가능하다.

> log4j2.xml

- 경로 src/main/resource

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%-5p] [%X{sessionID}] (%-35c{1}:%-3L) %m%n" />
        </Console>
    </Appenders>
    
    <Loggers>
        <!-- Application Loggers -->
	    <logger name="neo"                  level="DEBUG" additivity="false"><AppenderRef ref="console" /></logger>
        
	    <!-- FATAL, ERROR, WARN, INFO, DEBUG, TRACE -->        
        <Root level="ERROR">
            <AppenderRef ref="console" />
        </Root>
    </Loggers>
</Configuration>
```

<br><br>


# 5. 로그 출력

예전에 앞서 Lombok를 통해 @Slf4j 애너테이션으로도 로거를 대체해서 로그를 출력할 수 있다.

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@RequestMapping("/board")
public class BoardController {
	
	private static final Logger logger = LoggerFactory.getLogger(BoardController.class);
```

```java
import lombok.extern.slf4j.Slf4j;

@RequestMapping("/board")
@Slf4j
public class BoardController {
	
    // ...

    if (log.isDebugEnabled()) {
        log.debug("test log");
    }
```

<br><br>

> 다시 한번 기억하면, `SLF4J 라는 추상화된 인터페이스로 로그 코드를 작성하고, 스프링 주입을 통해서 실제 구현체 Logging Framework에서 로그 코드를 구현해서 출력한다.`
<br><br>이어서 쿼리 로그 설정을 이어가겠습니다.
