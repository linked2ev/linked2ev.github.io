---
layout: post
title:  "14. 스프링부트 MVC - Spring AOP 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

스프링에서 AOP는 중요하다. 그 만큼 내용이 방대하고 깊이가 있기에 한번에 포스팅하기에는 무리가 있다. 그래서 우선적으로 가볍게 포스팅을 정리했다.

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
- #Lombok #Log4j2 #DAO #Mapper #Filter #Interceptor #AOP

---

> 참고 URL

---

<br><br>


# AOP이란?

AOP는 `Aspect Oriented Programming`의 약자로 `관점 지향 프로그래밍`이다. DI처럼 애플리케이션 객체 간의 결합도를 낮추는데 기여한다. AOP는 주로 애플리케이션 전체에 걸친 횡단관심사(트랜잭션이나 보안 등)와 객체 간의 결합도를 낮추는거고.

코드로 말하면, `비즈니스 로직 객체`와 권한, 트랜잭션, 예외처리, 로깅 등 `공통적인 부분`을 `분리`하여 처리 하는 것이다.

다시 말하면, `개발자는 기능에 대한 비즈니스 로직에만 집중하게끔` 공통 모듈을 분리해서 횡단 관점 개념으로 주입하는 거다.

<br>

## 개념은 이해는 갔는데.. `그래서 AOP가 뭔데요??`

Java의 OOP(Object Oriented Programming)를 더욱 OOP답게 사용할 수 있도록 도와주며, Java 진영의 AspectJ라는 AOP 프레임워크를 Spring에서 프록시 기반으로 Spring AOP를 제공하는 것이다.

스프링 AOP는 `프록시(Proxy) 패턴`을 통해서 구현되어 있는데 외부에서 특정한 객체를 호출하면, 객체를 감싸고 있는 바깥쪽 객체(Proxy)를 통해서 호출이 되는 방식이다.

컴파일하거나 컴파일 된 클래스를 로딩하는 시점에 AOP가 적용되어 개발자가 구현한 비즈니스 로직 코드 사이에 공통 모듈 코드가 삽입되는 개념이다.

<br>

[![sts-mvc-aop-14-s1](/assets/img/devlog/201909/sts-mvc-aop-14-s1.png)]()

<br>

AOP는 위에 도식표처럼 `핵심 비즈니스 기능과 공통 기능을 "구분"헤서, 공통 기능을 개발자의 코드 밖에서 필요한 시점에 적용하는 프로그래밍 방법`이다.

사실, 만들어 놓고 로그 찍고 용어정리하면 더 이해가 쉽다...

<br><br>


# AOP 설정

> build.grade

- dependency 추가

```gradle
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-aop'
} 
```

<br>

@Component를 지정해 자동스캔하게끔 해서 빈으로 등록하고
애스펙트는 자바 클래스에 @Aspect 애너테이션을 지정해 포인트컷과 어드바이스를 정의 한다.

> LogerAspect.java

- neo.com.aop

```java
@Slf4j
@Component
@Aspect
public class LoggerAspect {

	static String className = "";
	static String methodName = "";
	
	
	@Pointcut("execution(* neo..apps..controller.*Controller.*(..))")
	public void presentationLayer() {
	}

	@Pointcut("execution(* neo..apps..service.*Impl.*(..))")
	public void serviceLayer() {
	}

	@Pointcut("execution(* neo..apps..mapper.*Mapper.*(..))")
	public void persistenceLayer() {
	}

	@Before("presentationLayer()")
	public void presentation(JoinPoint joinPoint) {
		//log.info("[Controller] : {}()", joinPoint.getSignature().getName());
	}
	
	@Around("serviceLayer()")
	public Object service(ProceedingJoinPoint joinPoint) throws Throwable {
		long start = System.currentTimeMillis();

		try {
			Signature signature = joinPoint.getSignature();
			className = joinPoint.getTarget().getClass().getName();
			methodName = signature.getName().toString();
			
			log.info("[ServiceImpl] : {}.{}()", className, methodName);
			//핵심 로직 이동
			Object result = joinPoint.proceed();
			
			return result;
		
		} finally {
			long end = System.currentTimeMillis();
			log.info(joinPoint.getSignature().toShortString() + " runtime : " + (end - start) + "ms");
		}
	}
	
	@Around("persistenceLayer()")
	public Object persistence(ProceedingJoinPoint joinPoint) throws Throwable {
		
		//Signature signature = joinPoint.getSignature();
		//className = signature.getDeclaringTypeName();
		//methodName = signature.getName().toString();
		
		//log.info("[Mapper] : {}.{}()", className, methodName);

		return joinPoint.proceed();
	}
}
```

# AOP 용어


## `타겟 (Target)`
---

어드바이스를 받을 대상 즉, 핵심기능이 담긴 클래스이다. 

<br>

## `애스펙트 (Aspect)`
---

스프링은 스캔하여 @Aspect 어노테이션을 포함한 클래스를 검색하여 Spring AOP 설정에 반영한다.

공통적으로 적용 될 기능을 의미하여, 횡단관심사 기능으로 볼 수 있다. 한 개 이상의 포인트컷과 어드바이스로 이루어진다. Advice를 적용하도록 지원할 수 있는 것을 Aspect라고 한다.

<br>


## `어드바이스 (Advice)`
---

충고(Advice)는 관점(Aspect)의 실제 기능 구현체로 포인트컷 표현식과 일치하는 결합점에 삽입되어 동작할 수 있는 코드이다. 즉, `횡단관심사`이다.

어드바이스는 애스펙트가 `무엇`을 `언제` 할지를 정의하며, 결합점과 결합하여 동작하는 시점에 따라 @Before, @After, @Around  등으로 구분된다.

애너테이션 | 설명
---- | ----
@Before | 메소드 실행 전 기능 수행
@After | 메소드 결과와 상관없이 메소드가 완료 된 이후에 기능 수행
@AfterReturning | 메소드가 성공적으로 완료 된 이후에 기능 수행
@AfterThrowing | 메소드 수행 중 예외 발생 시 이후에 기능 수행
@Around | proceed() 메소드 호출 전, 후로 통해 기능 구분 수행

<br>

## `조인포인트 (JoinPoint)`
---

어드바이스를 적용하는 `지점`을 얘기한다. 즉, 어플리케이션에 실행에 애스펙트 코드를 넣을 지점을 말한다.

Spring에서는 메소드 조인포인트만 제공하고 있다.

<br>

## `포인트커트 (Pointcut)`  
---

어드바이스가 적용 될 지점을 Pointcut 표현식에 맞고, JoinPoint에 해당하는 적용할 대상을 선별하여 `어디서`할지를 정의한다.

참고 - [포인트컷 지정자/표현식 설명](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte:fdl:aop:aspectj)

<br>

## `위빙 (Weaving)`
---

지정된 객체에 애스팩트를 적용해서 새로운 프록시 객체를 생성하는 과정을 얘기합니다. 어드바이스를 핵심로직코드에 적용하는 것을 weaving이라고 한다. 즉 `어드바이스와 타켓이 결합되어서 프록시 객체를 만드는 과정`이다.

<br>

> 프록시 (Proxy)

타겟을 감싸서 타겟의 요청을 대신 받아주는 랩핑(Wrapping) 오브젝트입니다. 어드바이스가 적용되었을 때 만들어지는 객체.




<br><br><br><br>


추후에 AOP에 대해서 개념과 사용법, 부연 기능 등은 다시 스프링 포스팅을 진행하면서 작성 할 예정이다.


---
[참고]
- https://tram-devlog.tistory.com/entry/Spring-AOP-weaving-proxy
- https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte:fdl:aop:aspectj
- 코드로 배우는 스프링 웹 프로젝트(책)