---
layout: post
title:  "13. 스프링부트 MVC - Filiter 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

이번에는 스프링부트에서 Filter(필터) 설정이다. Encoding, XSS, CORS 이슈 그리고 HTTP 헤더 정보 등을 일괄적으로 설정하는 부분이기에 restful 한 프로젝트 개발할 경우 이해하기 좋을 수 있다.

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
- #Lombok #Log4j2 #DAO #Mapper #Filter #Interceptor

---

> 참고 URL

---

<br><br>


# Filter란?

[![sts-interceptor-12-01](/assets/img/devlog/201909/sts-interceptor-12-01.png)]()

필터란? 거른다는 뜻이다. 서블릿의 ServletContext 기능으로 사용자에 의해 서블릿이 호출 되긴 전/후로 사용자 요청/응답의 헤더 정보 등을 검사 및 설정할 수가 있다.

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


# 스프링부트에서 Filter 설정

스프링에서는 웹 어플리케이션 컨텍스트를 설정할 수 있는 web.xml 파일을 통한 필터를 설정한 부분이다.

> web.xml

```xml
<filter>
    <filter-name>CORSFilter</filter-name>
    <filter-class>neo.config.CORSFilter</filter-class>
</filter>
  
<filter-mapping>
    <filter-name>CORSFilter</filter-name>
    <url-pattern>/board/*</url-pattern>
</filter-mapping>
```


하지만, 스프링부트에서는 이제 web.xml 파일이 없어졌다. 대신 프로그래밍 방식으로 설정할 수 있으며 2가지 방법이 있다.

첫번째는 자바프로그래밍 방식으로 `FilterRegistrationBean`을 이용해서 필터로 등록한 후에 WebMvcConfigurer을 상속 받아 설정 후 빈으로 등록하는 방식

두번째는 내장 컨테이너를 사용하는 스프링부트에서 지원하는 애너테이션인 `@WebServlet`, `@WebFilter` 및 `@WebListener` 선언해서 자동 등록 후 `@ServletComponentScan`로 이용하는 방식

XSS, CORS 처리하는 필터라는 가정하에 CORSFilter는 첫번째 방식으로 XSSFilter는 애너테이션 방식으로 다르게 설정해보겠다.

<br>


## 방법 1) FilterRegistrationBean으로 필터 등록
---

`org.springframework.boot.web.servlet`의 `FilterRegistrationBean`를 사용해 CORSFilter 클래스를 설정파일에서 빈으로 등록하는 방식이다.

> CORSFilter.java(example)

- neo.com.filiter

```java
@Slf4j
public class CORSFilter implements Filter {
	
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		log.info("init CORSFilter");
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse res = (HttpServletResponse) response;
		
		log.info("##### filter - before #####");
		chain.doFilter(req, res);
		log.info("##### filter - after #####");
	}
	
	@Override
	public void destroy() {
		log.info("destroy CORSFilter");
	}
	
}
```

<br>

> FilterConfiguration.java

- neo.config

```java
@Configuration
public class FilterConfiguration implements WebMvcConfigurer {
	
	@Bean
	public FilterRegistrationBean getFilterRegistrationBean() {
		FilterRegistrationBean registrationBean = new FilterRegistrationBean(new CORSFilter());
		registrationBean.setOrder(Integer.MIN_VALUE);
		//registrationBean.addUrlPatterns("/*");
		registrationBean.setUrlPatterns(Arrays.asList("/board/*"));
		return registrationBean;
	}
}
```

<br>


## 방법 2) @WebFilter 애너테이션 필터 등록
---

스프링부트에서 지원하는 `@WebFilter` 애너테이션으로 자동 등록 후에 `@ServletComponentScan`으로 사용하는 방식이다.

<br>

> XSSFilter.java(example)

- neo.com.filiter

```java
@Slf4j
@WebFilter(urlPatterns= "/board/*")
public class XSSFilter implements Filter {
	
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		log.info("init XSSFilter");
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse res = (HttpServletResponse) response;
	
		log.info("##### filter - before #####");
		chain.doFilter(req, res);
		log.info("##### filter - after #####");
	}
	
	@Override
	public void destroy() {
		log.info("destroy XSSFilter");
	}
	
}
```

```java
@ServletComponentScan // <-추가
@SpringBootApplication
public class NeoApplication {

	public static void main(String[] args) {
		SpringApplication.run(NeoApplication.class, args);
	}

}

```

<br><br>

> 필터 내 메서드 설명

- init()
	+ 웹 컨테이너(톰캣)이 시작될 때 필터 최초 한 번 인스턴스 생성
- doFilter()
	+ 클라이언트의 요청 시 전/후 처리
	+ FilterChain을 통해 전달
- public void destroy()
	+ 필터 인스턴스가 제거될 때 실행되는 메서드, 종료하는 기능

<br>

> 필터 제외 URL

- 스프링 Context내의 인터셉터처럼 특정 URL패턴을 제외시키는 설정 기능은 없다.
	+ 제외 URL패턴 필요시 프로그래밍으로 작성해야함

<br><br>


아래는 필터 등록 후 사용자 요청 시에 출력되는 로그이다.

[![sts-filter-13-01](/assets/img/devlog/201909/sts-filter-13-01.png)]()
