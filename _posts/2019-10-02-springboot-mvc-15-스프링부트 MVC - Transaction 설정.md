---
layout: post
title:  "15. 스프링부트 MVC - Transaction 선언적 방식 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

이번에는 트렌잭션 설정에 대해서 진행하겠습니다.

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
- #Lombok #Log4j2 #DAO #Mapper #Filter #Interceptor #AOP #Transaction

---

> 요즘은 rest방식인 MSA나 대용량 데이터에 쓰이는 NoSQL에서는 강한결합 형태로 트랜잭션 처리를 안한다. 우선 현재 프로젝트는 RDBMS를 이용한 일반적인 방식이기에 설정 위주로 진행하겠습니다.
<br><br>추후 스프링 카테고리와 rest방식인 MSA구조까지 진행이 될 때 주제에 맞게 진행 할 포스팅 할 예정

---

<br><br>

# 트랜잭션이란

트랜잭션(Transaction)은 데이터베이스의 상태를 변환시키는 하나의 논리적 기능을 수행하기 위한 작업의 단위 또는 한꺼번에 모두 수행되어야 할 일련의 연산들을 의미한다.

<br>

# 트랜잭션의 특징

ACID | 설명
---- | ----
Atomicity(원자성) | 트랜잭션의 연산은 데이터베이스에 모두 반영되든지 아니면 전혀 반영되지 않아야 한다.(All or Nothing) 
Consistency(일관성) | 트랜잭션이 성공적으로 완료하면 일관성 있는 데이터베이스 반영한다.
Isolation(독립성,격리성) | 트랜잭션은 독립적으로 처리되며, 서로간의 트랜잭션은 간섭(연산) 할 수 없다.
Durablility(영속성,지속성) | 성공적으로 완료된 트랜잭션의 결과는 영구적으로 지속되어야 한다.

<br>

# 선언적 트랜잭션

스프링에서 트랜잭션을 설정하는 방식으로 2가지 방법이 있다. 코드 기반 방식과 `설정파일` 또는 `@Transactional 애너테이션`을 이용한 선언적 트랜잭션 방식 있는데 해당 포스팅에서는 `선언적 트랜잭션 방식`으로 진행하겠습니다.

선언적 트랜잭션은 설정 파일이나 어노테이션을 이용해서 트랜잭션의 범위, 롤백 규칙 등을 정의하여 처리 한다.

<br>


## `@Transaction 애너테이션`을 이용한 방식
---

- @Transactional 애너테이션을 이용하여 원하는 메서드나 클래스 등에 속성을 주어 트랜잭션을 기능 구현에 맞게 적용할 수 있다.

<br>

> 설정파일 xml

```xml
<!-- Transaction 설정 -->
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

> 비즈니스 로직 메서드

```java
@Transactional(propagation=Propagation.REQUIRED)
public int insertBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
```

<br>


## `AOP`를 이용한 방식
---

- AOP를 이용해 트랜잭션의 범위, 롤백 규칙 등을 설정파일로 공통적으로 트랜잭션을 적용하는 방식이다.
- 각 구현체에 비즈니스 로직을 구현할 때 따로 트랜잭션 미처리에 대한 문제를 피할 수 있다.
- 필요없는 부분까지 적용이 되기에 성능에 문제가 될 수 있다.

<br>

> 설정파일 xml

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
```

<br><br>


# JavaConfig 방식으로 트랜잭션 설정

현재 진행되는 스프링부트 프로젝트에 JavaConfig 방식으로 트랜잭션을 설정 진행하겠습니다.

<br>

## `@Transaction 애너테이션`을 이용한 방식
---

XML 네임스페이스에있는 `<tx:*>`과 유사한 스프링의 애너테이션 중심 트랜잭션 관리 기능을 사용하기 위해 `@Configuration`을 선언 된 Database 자바설정파일에 스프링에서 제공하는 어노테이션 트랜잭션인 `@EnableTransactionManagement`를 추가해서 트랜잭션 매니저를 아래와 같이 설정한다.

<br>

> DatabaseConfiguration.java

- package: neo.config 

```java
@Configuration
@PropertySource("classpath:/application.properties")
@EnableTransactionManagement
@Slf4j
public class DatabaseConfiguration {
	
	// ... 생략 ...

	@Bean
	public PlatformTransactionManager txManager() throws Exception {
		return new DataSourceTransactionManager(dataSource());
	}
}
```

<br>

이와 같이 설정을 하면 xml 설정파일과 같은 설정이며, 비즈니스 구현체 클래스나 메서드에 `@Transactional`를 선언하여 트랜잭션 처리를 할 수 있다.

> 비즈니스 로직 메서드

```java
@Transactional(propagation=Propagation.REQUIRED)
public int insertBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
```

<br>

## `AOP`를 이용한 방식
---

추가적으로 위에 xml설정파일 처럼 AOP를 활용해서 트랜잭션을 설정하려면 아래와 같이 선언하면 된다.

> TransactionAspectConfiguration.java

- package : neo.config

```java
@Configuration
public class TransactionAspect {
	
	@Autowired
	PlatformTransactionManager transactionManager;

	@Bean
	public TransactionInterceptor transactionAdvice() {
		NameMatchTransactionAttributeSource txAttributeSource = new NameMatchTransactionAttributeSource();
		RuleBasedTransactionAttribute txAttribute = new RuleBasedTransactionAttribute();
		
		txAttribute.setRollbackRules(Collections.singletonList(new RollbackRuleAttribute(Exception.class)));
		txAttribute.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
		
		HashMap<String, TransactionAttribute> txMethods = new HashMap<String, TransactionAttribute>();
		txMethods.put("*", txAttribute);
		txAttributeSource.setNameMap(txMethods);

		return new TransactionInterceptor(transactionManager, txAttributeSource);
	}

	@Bean
	public Advisor transactionAdviceAdvisor() {
		AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
		pointcut.setExpression("execution(* neo..apps..service.*Impl.*(..))");
		return new DefaultPointcutAdvisor(pointcut, transactionAdvice());
	}
	
}
```

<br><br>

이와 같이 설정하면 된다. 이제 트랜잭션 설정이 된 구현체에서 강제로 에러를 발생시켜서 확인해보면 된다.

```java
public BoardDto selectBoard(HttpServletRequest request, int boardIdx) throws Exception {
	boardMapper.updateBoardViewsHit(boardIdx);
	int a = 1/0;
	return boardMapper.selectBoard(boardIdx);
}
```

<br>

세부적인 설정이나 설명은 참고 URL에 API문서를 링크를 걸어놨다. 위와 다른 설정 방법도 있어서 구글링을 통해 알아보는 것도 좋을 듯 하다.

다음 포스팅은 트랜잭션 대한 설명 및 속성에 대해서 정리 할 예정이다.

<br><br>

---
[참고]

- https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/transaction.html
- https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/transaction/PlatformTransactionManager.html
- https://docs.spring.io/spring/docs/3.2.x/javadoc-api/org/springframework/transaction/annotation/EnableTransactionManagement.html
- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/aop/support/DefaultPointcutAdvisor.html
- http://mybatis.org/spring/ko/transactions.html
- [https://github.com/pengsoftdotcom - 코드 참고](https://github.com/pengsoftdotcom/projects/blob/fa5579a9239ed417c47a69508d1f883599d805fa/pengsoft-support/pengsoft-support-starter/src/main/java/com/pengsoft/support/starter/autoconfigure/SupportModuleAutoConfigure.java)