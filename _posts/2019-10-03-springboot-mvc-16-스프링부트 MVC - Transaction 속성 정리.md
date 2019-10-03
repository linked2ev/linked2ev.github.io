---
layout: post
title:  "16. 스프링부트 MVC - Transaction 속성 정리"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

해당 포스팅에서는 트랜잭션 속성을 정리하기 위해 작성했다.

<br><br>


---

> 관련 포스팅

- [15. 스프링부트 MVC - Transaction 선언적 방식 설정](https://linked2ev.github.io/gitlog/2019/10/02/springboot-mvc-15-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Transaction-%EC%84%A4%EC%A0%95/)

---

<br>

# context-transaction.xml 샘플

```xml
<!-- Transaction 설정 -->
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>

<tx:advice id="txAdvice" transaction-manager="txManager">
	<tx:attributes>
		<tx:method name="*" rollback-for="Exception"/>
	</tx:attributes>
</tx:advice>

<!-- Transaction을 위한 AOP 설정 -->
<aop:config>
	<aop:pointcut id="requiredTx" expression="execution(* neo..apps..service.*Impl.*(..))"/>
	<aop:advisor advice-ref="txAdvice" pointcut-ref="requiredTx" />
</aop:config>

<tx:annotation-driven />
```

<br>


# `name 메서드명`

메서드의 이름이며 와일드 문자(*)를 사용할 수 있습니다.


```xml
name="*"
name="get*"
name="select*"
name="insert*"
name="delete*"
```

<br><br>

# `Propagation(전파) 속성`

@Transactional의 propagation 속성을 이용해 호출한 쪽의 트랜잭션을 그대로 사용할 수도 있고, 새롭게 트랜잭션을 생성할 수도 있다. 트랜잭션의 경계를 정의하며 시작 방법을 결정하는 속성이다.

```java
@Transactional(propagation = Propagation.REQUIRED)
```

<br>

> `REQUIRED` (기본설정)

- 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 새로운 트랜잭션을 생성한다.

> `REQUIRES_NEW`

- 부모 트랜잭션을 무시하고 무조건 새로운 트랜잭션이 생성, 이미 진행중인 트랜잭션이 있다면 잠깐 보류되고 해당 트랜잭션 경계가 종료 된 후 다시 시작된다.

> `SUPPORTS`

- 이미 진행중인 부모 트랜잭션이 있으면 참여하고, 없을 경우 Non-transaction으로 실행된다.

> `NOT_SUPPORTED`

- Non-transaction으로 시작하며, 이미 진행중인 트랜잭션이 있으면 끝날 때까지 보류된 후 실행된다.

> `MANDATORY`

- 이미 진행중인 트랜잭션이 반드시 있어야만 실행되며, 혼자서는 독립적으로 트랜잭션을 진행하면 안 되는 경우에 사용한다.

> `NEVER`

- Non-transaction으로 시작하며, 실행되며 부모 트랜잭션이 존재한다면 예외가 발생합니다.

> `NESTED`

- 이미 진행중인 부모 트랜잭션이 있을 경우 중첩 트랜잭션을 생성하여 실행되며, 생성된 중첩 트랜잭션은 부모 트랜잭션이 rollback되면 함께 되지만, 해당 트랜잭션안에서의 Commit/Rollback은 부모 트랜잭션에 영향을 주지 않는다.


<br><br>



# `Isolation(격리) 속성`

트랜잭션에서 일관성이 없는 데이터를 허용하도록 하는 수준을 말한다.

```java
@Transactional(isolation=Isolation.DEFAULT)
```

<br>

> `DEFAULT` (기본설정)

- 기본 격리 수준(, DB의 Isolation Level을 따름)

> `READ_UNCOMMITTED`

 - 커밋되지 않는(트랜잭션 처리중인) 데이터에 대한 읽기를 허용

> `READ_COMMITTED`

 - 트랜잭션이 커밋 된 확정 데이터만 읽기 허용

> `REPEATABLE_READ`

- 하나의 트랜잭션이 읽은 로우를 다른 트랜잭션이 수정하는 것을 막아준다. 선행 트랜잭션이 읽은 데이터는 트랜잭션이 종료될 때까지 후행 트랜잭션이 갱신/삭제를 보류한다.

> `SERIALIZABLE`

- 가장 강력한 격리수준이다. 순차적으로 진행해서 여러 트랜잭션이 동시에 같은 테이블 정보를 엑세스하지 못하게 한다.

<br><br>


# `readOnly 속성`

트랜잭션을 읽기 전용으로 설정할 수 있으며, 트랜잭션이 시작된 이후 INSERT, UPDATE, DELETE 같은 쓰기 작업이 진행되면 예외가 발생한다. 

```java
@Transactional(readOnly = true)
```

<br><br>


# `timeout 속성`


지정한 시간 내에 해당 메소드 수행이 완료되이 않은 경우 rollback 수행

```java
@Transactional(timeout=10)
```

<br><br>


# `rollback 예외`

선언적 트랜잭션에서는 예외가 발생의 롤백 룰을 정의해서 런타임 에러 발생 시에 롤백한다.

```java
@Transactional(rollbackFor=Exception.class)
```

> `rollbackFor` 속성

- 특정 예외가 발생 시 강제로 Rollback

> `noRollbackFor` 속성

- 특정 예외의 발생 시 Rollback 처리되지 않음

<br><br>


---
[참고]
- http://blog.breakingthat.com/2018/04/03/springboot-transaction-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98/