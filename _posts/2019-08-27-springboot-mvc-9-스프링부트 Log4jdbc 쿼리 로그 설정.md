---
layout: post
title:  "9. 스프링부트 Log4jdbc 쿼리 로그 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

작성 중 (스프링부트 Log4j2 JDBC 쿼리 로그 설정) 

<br><br>


> 개발 환경  

- OS: Window 10
- Dev Tool: Eclipse IDE 2019-06
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.7
- Build Tool: Gradle v3
- RDBMS: MariaDB
	+ DBCP: hikariCP
	+ Persistence Framework: MyBatis
- #Lombok #Log4j2

<br><br>


1. Log4jdbc로 쿼리 로그 정렬 적용

스프링부트에서는 기본적으로 로그에 대한 설정이 되어있긴 하다. 외부라이브러리를 의존성을 추가해 설정하면 개발에 더 편리하며 효율성이 증가한다.

각 스프링 웹 프로젝트 설정 마다 다르겠지만, 쿼리에 대한 설정이 없으면 아래와 같이 콘솔에 출력된다. 약간 복잡한 쿼리일 경우는 가독성이 떨어져 개발자가 [사이트를 통해 쿼리(Mybatis Log Parser)](http://tipjs-team.github.io/tipJS/examples/myBatisLogParser/) 파싱 후 DB 툴로 데이터를 확인한다.

```
Preparing: SELECT * FROM T_BOARD WHERE BOARDIDX = ?
Parmeters: 3(Integer)
```

> 사내 자체적으로 릴리즈가 되지 않을 경우 아직도 log4j를 사용하거나 쿼리 정렬 등 을 사용하지 않는다. 이미 개발이 된 자바 코드들은 SLF4J 추상화를 통해서 작업이 되어있기에 변경없이 스프링 log4j2 + slf4j 연동 의존성 추가하고 log4j2.xml 설정파일로 대체가 가능하며, 쿼리 정렬 또한 Log4jdbc-remix로 가능하다.

스프링에서 간단하게 log4j를 log4j2로 쿼리 정렬를 도와주는 log4jdbc-remix라는 라이브러리가 있다.


```xml
<!-- logFormatter -->
<bean id="dataSource" class="net.sf.log4jdbc.Log4jdbcProxyDataSource">
    <constructor-arg ref="dataSource-${Globals.DbType}" />
    <property name="logFormatter">
        <bean class="net.sf.log4jdbc.tools.Log4JdbcCustomFormatter">
            <property name="loggingType" value="MULTI_LINE" />
            <property name="sqlPrefix" value="SQL         :  "/>
        </bean>
    </property>
</bean>
```

```xml
<dependency>
    <groupId>org.lazyluke</groupId>
    <artifactId>log4jdbc-remix</artifactId>
    <version>0.2.7</version>
</dependency>
```

<br><br>


하지만, 지금 log4jdbc-remix는 개발이 중단되고 현재는 log4jdbc-log4j2로 지원해준다. 

스프링부트에 쿼리 로그를 위한 log4jdbc-log4j2를 적용하기 위해서는 설정 파일을 변경해준다.

> application.properties

```gradle
spring.datasource.hikari.driver-class-name=net.sf.log4jdbc.sql.jdbcapi.DriverSpy
spring.datasource.hikari.jdbc-url=jdbc:log4jdbc:mysql://localhost:3307/test?useUnicode=true&characterEncoding=utf-8
```

> log4jdbc.log4j2.properties (파일 생성)

- 경로: /src/main/resource

```gradle
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
log4jdbc.dump.sql.maxlinelength=0
```

> log4j2.xml (파일 수정)

- 경로: /src/main/resource

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
    
		<!-- jdbc.sql Loggers -->
        <Logger name="jdbc.connection"      level="WARN" additivity="false"><AppenderRef ref="console" /></Logger>
        <Logger name="jdbc.audit"           level="WARN" additivity="false"><AppenderRef ref="console" /></Logger>
        <Logger name="jdbc.sqlonly"         level="INFO" additivity="false"><AppenderRef ref="console" /></Logger>
        <Logger name="jdbc.sqltiming"       level="INFO" additivity="false"><AppenderRef ref="console" /></Logger>
        <Logger name="jdbc.resultset"       level="WARN" additivity="false"><AppenderRef ref="console" /></Logger>
        <Logger name="jdbc.resultsettable"  level="INFO" additivity="false"><AppenderRef ref="console" /></Logger>
        
	    <!-- FATAL, ERROR, WARN, INFO, DEBUG, TRACE -->        
        <Root level="ERROR">
            <AppenderRef ref="console" />
        </Root>
    </Loggers>
</Configuration>
```

<br><br>


커넥션이 있는 요청을 하면 아래와 같이 쿼리에 대한 정렬 및 정보를 콘솔에 출력된다.

<br>

[![sts-mvc-9-01](/assets/img/devlog/201908/sts-mvc-9-01.png)]()

<br><br>


# #로거 옵션 설명

로거 옵션 | 설명
---- | ---- 
jdbc.sqlonly | SQL문만을 로그로 남기며, PreparedStatement일 경우 관련된 argument 값으로 대체된 SQL문이 출력 
jdbc.sqltiming | SQL문과 해당 SQL을 실행시키는데 수행된 시간 정보(milliseconds)를 출력
jdbc.audit | ResultSet을 제외한 모든 JDBC 호출 정보를 로그로 남긴다. 많은 양의 로그가 생성되므로 특별히 JDBC 문제를 추적해야 할 필요가 있는 경우를 제외하고는 사용을 권장하지 않는다. 
jdbc.resultset | ResultSet을 포함한 모든 JDBC 호출 정보를 로그로 남기므로 매우 방대한 양의 로그가 생성된다. 
jdbc.resultsettable | SQL 결과 조회된 데이터의 table을 로그로 남긴다.
jdbc.connection | 커넥션 연결과 종료에 관련 된 로그를 노출

<br><br>

---
[참고]
- https://lee-mandu.tistory.com/352?category=715433