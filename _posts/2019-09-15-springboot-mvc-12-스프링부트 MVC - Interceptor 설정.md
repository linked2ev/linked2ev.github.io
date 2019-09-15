---
layout: post
title:  "12. 스프링부트 MVC - Interceptor 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

이번에는 스프링부트에서 Interceptor(인터셉터) 설정이다. 개인적인 생각이지만 권한과 인증 등 관련 된 부분이기에 중요하고, 웹 어플리케이션을 개발하거나 파악하기 편하다.

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
- #Lombok #Log4j2 #DAO #Mapper #Interceptor

---

> 참고 URL

---

<br><br>


# Interceptor란?

[![sts-interceptor-12-01](/assets/img/devlog/201909/sts-interceptor-12-01.png)]()

인터셉터란? 가로챈다는 뜻이다. 스프링의 Spring Context(ApplicationContext)기능으로 인터셉터는 임의의 URI를 호출시 DispatcherServlet에서 해당 Controller가 처리 되기 전과 후에 발생하는 이벤트이다.
비슷한 역할로는 필터(Filiter)가 있다. 둘다 역할은 비슷하지만 차이가 있다.

<br>


# Filter와 Interceptor 비교

- 필터는 DispatcherServlet 앞에서 먼저 동작하고, 인터셉터는 DispatcherServlet에서 Controllr(Handler) 사이에서 동작한다.
- 필터
	+ `웹 어플리케이션의 Context`의 기능
	+ 스프링 기능을 활용하기에 어려움
	+ 일반적으로 인코딩, CORS, XSS, LOG, 인증, 권한 등 을 구현
- 인터셉터
	+ `스프링의 Spring Context`의 기능이며 일종의 빈
	+ 스프링 컨테이너이기에 다른 빈을 주입하여 활용성이 좋음
	+ 다른 빈을 활용 가능하기에 인증, 권한 등을 구현함

<br><br>


# Interceptor 추가 및 설정

## dependency
---

스프링부트에서는 spring-webmvc가 spring-boot-starter-web에 포함되어 있다.

```gradle
dependencies { 
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

<br>

## LoggerInterceptor 생성
---

HandlerInterceptor 인터페이스를 이용한 방식과 HandlerInterceptorAdapter 추상 클래스를 상속받아 설정하는 방식이 있는데 HandlerInterceptorAdapter를 상속받아서

사용자가 요청 할 경우 접근 정보 로그 등을 확인하는 Interceptor를 LoggerInterceptor라는 클래스를 생성

<br>

> LoggerInterceptor.java

- neo.com.interceptor

```java
@Slf4j
@Component
public class LoggerInterceptor extends HandlerInterceptorAdapter {

	//controller로 보내기 전 이벤트 작동(false - controller로 요청을 안함)
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		log.debug("============================== START ==============================");
		log.info(" Class       \t:  " + handler.getClass());
		log.info(" Request URI \t:  " + request.getRequestURI());
		log.info(" Servlet URI \t:  " + request.getServletPath());
		
		Enumeration<String> paramNames = request.getParameterNames();
		
		while (paramNames.hasMoreElements()) {
			String key = (String) paramNames.nextElement();  
			String value = request.getParameter(key);
			log.info("# RequestParameter: " + key + "=" + value + "");
		}
		log.info("==================================================================== ");

		return super.preHandle(request, response, handler);
	}
	
	//controller 처리 이후 이벤트 작동
	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
		//log.info("================================ END ================================");
	}
	
	//view 처리 이후 이벤트 작동
	@Override
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
		log.info("================================ END ================================");
	}
}
```

- preHandle()
	+ controller로 보내기 전 이벤트 작동
- postHandle()
	+ controller 처리 이후 이벤트 작동, DispatcherServlet이 View처리 이전
- afterCompletion()
	+ DispatcherServlet이 View까지 완료된 상태에서 처리

<br><br>


# 스프링부트 Interceptor 등록

빈으로 등록 된 loggerInterceptor를 주입하여 설정하는 부분이다. neo.config 패키지 경로에 @Configuration을 지정해서 WebMvcConfiguration.java 설정 클래스를 생성

> WebMvcConfiguration.java

- neo.config

```java
@Configuration
public class WebMvcConfiguration implements WebMvcConfigurer {
	
	@Autowired
	LoggerInterceptor loggerInterceptor;
	
	@Override
	public void addInterceptors (InterceptorRegistry registry) {
		registry.addInterceptor(loggerInterceptor).excludePathPatterns("/assets/**");
	}
}
```

<br>

스프링 프로젝트에서 xml방식으로 Interceptor를 설정할 경우 아래와 같다. 그리고 xml에서 빈을 등록하기 때문에 LoggerInterceptor 클래스에서는 @Component 애너테이션을 지워야한다.

> Servlet-context.xml

```xml
<resources mapping="/assets/**" location="/assets/" />

<mvc:interceptors>
 	<mvc:mapping path="/*"/>
	<beans:bean class="neo.config.interceptor.LoggerInterceptor" />
</mvc:interceptors>
```

<br>

아래는 요청을 했을 때 인터셉터에서 로그 부분이다.


[![sts-interceptor-12-02](/assets/img/devlog/201909/sts-interceptor-12-02.png)]()
