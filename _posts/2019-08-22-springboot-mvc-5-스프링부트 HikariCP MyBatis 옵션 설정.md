---
layout: post
title:  "5. 스프링부트 HikariCP MyBatis 옵션 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

스프링부트 HikariCP MyBatis 추가 설정이다.

<br>


## HikariCP 설정

HikariCP + MySQL 관련 추가 설정하는 방법이다.

> application.properties

```gradle
spring.datasource.hikari.pool-name=NEO-HikariCP //pool이름
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.data-source-properties.cachePrepStmts=true
spring.datasource.hikari.data-source-properties.prepStmtCacheSize=250
spring.datasource.hikari.data-source-properties.prepStmtCacheSqlLimit=2048
spring.datasource.hikari.data-source-properties.useServerPrepStmts=true
```

<br><br>


## MyBatis 설정
---

스프링 mybatis 추가 설정을 관리하기 위해 src/main/resource 경로에 "mybatis-config.xml" 파일를 생성한다.

> mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <settings>
        <setting name="cacheEnabled" value="true" />
        <setting name="useGeneratedKeys" value="false" />
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>
    <typeAliases>
    	<typeAlias type="neo.apps.board.dto.BoardDto" alias="BoardDto" />
    </typeAliases>
</configuration>
```

<br>

해당 mybatis-config.xml 설정을 사용하기 위해 DatabaseConfiguration.java에 아래와 같이 추가한다. (만약, Springboot이 지원하는 properties방식으로 사용할려면 setConfiguration 키워드로 구글검색을 비슷한 예제 찾을 수 있다.)

> 추가 코드

```java
sqlSessionFactoryBean.setConfigLocation(applicationContext.getResource("classpath:/mybatis-config.xml"));
```

> DatabaseConfiguration.java

```java
@Configuration
@PropertySource("classpath:/application.properties")
public class DatabaseConfiguration {
	
	@Bean
	public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
		SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
		sqlSessionFactoryBean.setDataSource(dataSource);
		sqlSessionFactoryBean.setMapperLocations(applicationContext.getResources("classpath:/mapper/**/*.xml"));
		sqlSessionFactoryBean.setConfigLocation(applicationContext.getResource("classpath:/mybatis-config.xml"));
		return sqlSessionFactoryBean.getObject();
	}
}
```


<br><br>


---

[참고]

- https://github.com/brettwooldridge/HikariCP#configuration-knobs-baby
- https://github.com/brettwooldridge/HikariCP/wiki/MySQL-Configuration
- http://www.mybatis.org/mybatis-3/ko/configuration.html
- http://www.mybatis.org/mybatis-3/ko/sqlmap-xml.html
- https://javacan.tistory.com/entry/spring-boot-2-hikaricp-property