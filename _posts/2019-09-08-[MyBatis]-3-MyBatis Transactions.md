---
layout: post
title:  "[MyBatis] MyBatis Transactions"
subtitle:   "[Mybatis]"
categories: mybatis
tags: mybatis-posting
comments: true
---

MyBatis Transactions 설명

<br>


## Transactions

- 마이바티스 스프링 연동모듈을 사용하는 중요한 이유
  + 마이바티스가 스프링 트랜잭션에 자연스럽게 연동 될 수 있다.
  + 마이바티스에 종속되는 새로운 트랜잭션 관리를 만드는 것보다는 마이바티스 스프링 연동모듈이 스프링의 DataSourceTransactionManager과 융합되는 것이 좋다.
  
- 스프링 트랜잭션 관리자를 한번 설정
  + 대개의 경우처럼 스프링에서 트랜잭션을 설정할 수 있다.
  + @Transactional 애노테이션과 AOP스타일의 설정 모두 지원한다. 하나의 SqlSession객체가 생성되고 트랜잭션이 동작하는 동안 지속적으로 사용되며, 트랜잭션이 완료되면 커밋이 되거나 롤백
마이바티스 스프링 연동모듈은 한번 셋업되면 트랜잭션을 투명하게 관리한다. DAO클래스에 어떠한 추가적인 코드를 넣을 필요가 없다. 

<br>

> 스프링 표준 설정

- 스프링 설정파일에 DataSourceTransactionManager를 생성
- 트랜잭션 관리자에 명시된 DataSource가 SqlSessionFactoryBean을 생성할때 사용된 것과 반드시 동일해야한다.

```xml
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <constructor-arg ref="dataSource" />
</bean>
```

```java
@Bean
public DataSourceTransactionManager transactionManager() {
  return new DataSourceTransactionManager(dataSource());
}
```

<br><br>


# 스프링 AOP Transactions

- 일반적인 스프링 AOP를 활용한 Transactions설정 일부분이다.

```xml
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <property name="dataSource" ref="dataSource"/>
</bean>

<tx:advice id="txAdvice" transaction-manager="txManager">
  <tx:attributes>
    <tx:method name="*" rollback-for="Exception"/>
  </tx:attributes>
</tx:advice>

<aop:config>
  <aop:pointcut id="requiredTx" expression="execution(* coom.example.example..impl.*Impl.*(..))"/>
  <aop:advisor advice-ref="txAdvice" pointcut-ref="requiredTx" />
</aop:config>
```


<br><br>

---
참고
- http://www.mybatis.org/spring/ko/transactions.html
