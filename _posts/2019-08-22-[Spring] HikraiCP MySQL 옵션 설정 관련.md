---
layout: post
title:  "[Spring] HikraiCP MySQL 옵션 설정 관련"
subtitle:   "[springboot]"
categories: spring
tags: spring-devhistory
comments: true
---

HikraiCP + MySQL 시에 옵션 설정에 대한 것만 원문 그대로 정리

<br><br>


# HikariCP 자주사용되는 옵션

- 출처: https://github.com/brettwooldridge/HikariCP#frequently-used

<br>

## autoCommit
---

This property controls the default auto-commit behavior of connections returned from the pool. It is a boolean value. Default: true

## connectionTimeout
---

This property controls the maximum number of milliseconds that a client (that's you) will wait for a connection from the pool. If this time is exceeded without a connection becoming available, a SQLException will be thrown. Lowest acceptable connection timeout is 250 ms. Default: 30000 (30 seconds)

## idleTimeout
---

This property controls the maximum amount of time that a connection is allowed to sit idle in the pool. This setting only applies when minimumIdle is defined to be less than maximumPoolSize. Idle connections will not be retired once the pool reaches minimumIdle connections. Whether a connection is retired as idle or not is subject to a maximum variation of +30 seconds, and average variation of +15 seconds. A connection will never be retired as idle before this timeout. A value of 0 means that idle connections are never removed from the pool. The minimum allowed value is 10000ms (10 seconds). Default: 600000 (10 minutes)

## maxLifetime
---

This property controls the maximum lifetime of a connection in the pool. An in-use connection will never be retired, only when it is closed will it then be removed. On a connection-by-connection basis, minor negative attenuation is applied to avoid mass-extinction in the pool. We strongly recommend setting this value, and it should be several seconds shorter than any database or infrastructure imposed connection time limit. A value of 0 indicates no maximum lifetime (infinite lifetime), subject of course to the idleTimeout setting. Default: 1800000 (30 minutes)

## connectionTestQuery
---

If your driver supports JDBC4 we strongly recommend not setting this property. This is for "legacy" drivers that do not support the JDBC4 Connection.isValid() API. This is the query that will be executed just before a connection is given to you from the pool to validate that the connection to the database is still alive. Again, try running the pool without this property, HikariCP will log an error if your driver is not JDBC4 compliant to let you know. Default: none

## minimumIdle
---

This property controls the minimum number of idle connections that HikariCP tries to maintain in the pool. If the idle connections dip below this value and total connections in the pool are less than maximumPoolSize, HikariCP will make a best effort to add additional connections quickly and efficiently. However, for maximum performance and responsiveness to spike demands, we recommend not setting this value and instead allowing HikariCP to act as a fixed size connection pool. Default: same as maximumPoolSize

## maximumPoolSize
---

This property controls the maximum size that the pool is allowed to reach, including both idle and in-use connections. Basically this value will determine the maximum number of actual connections to the database backend. A reasonable value for this is best determined by your execution environment. When the pool reaches this size, and no idle connections are available, calls to getConnection() will block for up to connectionTimeout milliseconds before timing out. Please read about pool sizing. Default: 10

## metricRegistry
---

This property is only available via programmatic configuration or IoC container. This property allows you to specify an instance of a Codahale/Dropwizard MetricRegistry to be used by the pool to record various metrics. See the Metrics wiki page for details. Default: none

## healthCheckRegistry
---

This property is only available via programmatic configuration or IoC container. This property allows you to specify an instance of a Codahale/Dropwizard HealthCheckRegistry to be used by the pool to report current health information. See the Health Checks wiki page for details. Default: none

## poolName
---

This property represents a user-defined name for the connection pool and appears mainly in logging and JMX management consoles to identify pools and pool configurations. Default: auto-generated

<br><br>


# HikariCP + MySQL 설정 시 

- 출처: https://github.com/brettwooldridge/HikariCP/wiki/MySQL-Configuration

## MySQL 권장 설정 일부
---

## prepStmtCacheSize
---

This sets the number of prepared statements that the MySQL driver will cache per connection. The default is a conservative 25. We recommend setting this to between 250-500.

## prepStmtCacheSqlLimit
---

This is the maximum length of a prepared SQL statement that the driver will cache. The MySQL default is 256. In our experience, especially with ORM frameworks like Hibernate, this default is well below the threshold of generated statement lengths. Our recommended setting is 2048.

## cachePrepStmts
---

Neither of the above parameters have any effect if the cache is in fact disabled, as it is by default. You must set this parameter to true.

## useServerPrepStmts 
---

Newer versions of MySQL support server-side prepared statements, this can provide a substantial performance boost. Set this property to true.

<br><br>


## HikariCP의 일반적인 MySQL 구성
---

```
jdbcUrl=jdbc:mysql://localhost:3306/simpsons
username=test
password=test
dataSource.cachePrepStmts=true
dataSource.prepStmtCacheSize=250
dataSource.prepStmtCacheSqlLimit=2048
dataSource.useServerPrepStmts=true
dataSource.useLocalSessionState=true
dataSource.rewriteBatchedStatements=true
dataSource.cacheResultSetMetadata=true
dataSource.cacheServerConfiguration=true
dataSource.elideSetAutoCommits=true
dataSource.maintainTimeStats=false
```

<br><br>


# HikariCP Datasource 설정 부분

- 기본적으로 `Datasource`를 이용한 설정 방법
- Springboot v2.x 이상부터 tomcat jdbc가 아닌 hikariCP 기본으로 지원

<br>

## 스프링부트 지원 properties를 이용한 설정 방법

```gradle
spring.datasource.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.hikari.jdbc-url=jdbc:mysql://localhost:3307/sample
spring.datasource.hikari.username=username
spring.datasource.hikari.password=password

spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.data-source-properties.cachePrepStmts=true
spring.datasource.hikari.data-source-properties.prepStmtCacheSize=250
spring.datasource.hikari.data-source-properties.prepStmtCacheSqlLimit=2048
spring.datasource.hikari.data-source-properties.useServerPrepStmts=true
```

```java
@Bean
@ConfigurationProperties(prefix="spring.datasource.hikari")
public HikariConfig hikariConfig() {
	return new HikariConfig();
}
```

<br>

## java 설정 방법

```java
@Bean
public HikariConfig hikariConfig() {
	HikariConfig hikariConfig = new HikariConfig();
	hikariConfig.setJdbcUrl(jdbcUrl);
	hikariConfig.setUsername(username);
	hikariConfig.setPassword(password);
	hikariConfig.setMaximumPoolSize(10);

	hikariConfig.addDataSourceProperty("cachePrepStmts", "true");
	hikariConfig.addDataSourceProperty("prepStmtCacheSize", "250");
	hikariConfig.addDataSourceProperty("prepStmtCacheSqlLimit", "2048");
	hikariConfig.addDataSourceProperty("useServerPrepStmts", "true");
	return hikariConfig;
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

