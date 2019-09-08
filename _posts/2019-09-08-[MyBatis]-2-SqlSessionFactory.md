---
layout: post
title:  "[MyBatis] SqlSessionFactory"
subtitle:   "[Mybatis]"
categories: mybatis
tags: mybatis-posting
comments: true
---

MyBatis에서 SqlSessionFactory에 대한 설명이다.

<br>


# SqlSessionFactoryBean

- Mybatis만 사용하면, SqlSessionFactory는 SqlSessionFactoryBuilder를 사용해서 생성한다. `Mybatis + Spring 연동모듈(라이브러리)에서는 SqlSessionFactoryBean가 대신 사용`된다.

<br>


## DataSource 설정
---

Mybatis에서는 팩토리 빈을 생성하기 위한 SqlSessionFactory는 DataSource를 필요로 한다.
<br>

자바에서 DataSource 객체는 ConnectionPool을 관리하는 목적으로 어플리케이션에서 Datasource 인터페이스를 통해서 Connection을 얻어오고 반납하는 등의 작업을 구현해야하는 인터페이스이다.

```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <property name="driverClassName" value="${Globals.DriverClassName}"/>
    <property name="url" value="${Globals.Url}" />
    <property name="username" value="${Globals.UserName}"/>
    <property name="password" value="${Globals.Password}"/>
</bean>
```

<br><br>


## SqlSessionFactory 빈 등록
---

SqlSessionFactoryBean 은 스프링의 FactoryBean 인터페이스를 구현한다는 점을 알아야 한다. 이 설정은 스프링이 SqlSessionFactoryBean 자체를 생성하는 것이 아니라 `팩토리에서 getObject() 메서드를 호출한 결과를 리턴한다는 것을 의미`한다.
<br>

스프링 IoC 컨테이너에서 애플리케이션 컴파일 시 SqlSessionFactoryBean 객체를 sqlSessionFactory 이름으로 빈을 등록한다.

<br>

> xml 설정 방식

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <property name="dataSource" ref="dataSource" />
</bean>
```

> java 설정 방식

```java
@Bean
public SqlSessionFactory sqlSessionFactory() {
  SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
  factoryBean.setDataSource(dataSource());
  return factoryBean.getObject();
}
```

<br><br>

---
참고
- http://www.mybatis.org/spring/ko/factorybean.html
