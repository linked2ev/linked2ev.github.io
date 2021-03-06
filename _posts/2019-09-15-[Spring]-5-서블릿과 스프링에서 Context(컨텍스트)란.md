---
layout: post
title:  "[Spring] 서블릿과 스프링에서 Context(컨텍스트)란?"
subtitle:   "[spring]"
categories: spring
tags: spring-posting
comments: true
---

WebApplicationContext? ServletContext? SpringContext?은 뭐고, 서블릿과 스프링에서 말하는 Context(컨텍스트)란?

<br><br>


# 스프링에서의 컨텍스트(Context) 란?

[![spring-context-s1](/assets/img/devlog/201909/spring-context-s1.png)]()


스프링에서 말하는 WebApplicationContext는 ApplicationContext를 확장한 WebApplicationContext 인터페이스의 구현체를 말하며, WebApplicationContext는 ApplicationContext를 상속받아서 사용한다.
스프링 컨테이너에서 관리하고 동작한다고 생각하면 된다.

DispatcherServlet이 여러 개일 경우 공통으로 사용할 빈들을 Root WebApplicationContext에 선언해두고 공유할 수 있게 하는 거죠.

<br><br>

```xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/root-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app1</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/app1-context.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app1</servlet-name>
        <url-pattern>/app1/*</url-pattern>
    </servlet-mapping>

</web-app>
```

## `Root WebApplicationContext`

- `ContextLoaderListener`에 의해 생성되는 `Root WebApplicationContext`이며, 자식 Context에서 Root Context를 참조할 수 있다.
- 여러 Servlet Context를 서로 공유해야 하는 빈들을 등록하고 설정할 때 사용

<br>

## `Servlet WebApplicationContext`

- `DispatcherServlet`에 의해 생성되는 자식 `WebApplicationContext`이며, Root Context에서는 상속 개념이기에 참조가 불가하다.
- 해당 Servlet Context에서만 사용이 가능하며, 스프링 DispatcherServlet에서 관리하는 독립적인 웹 어플리케이션 형태로 사용

<br>

## ContextLoaderListener 역할

- ContextLoaderListener 와 DispatcherServlet 은 각각 WebApplicationContext 를 생성하는데, 스프링에서 사용되는 Context 간의 계층 관계를 연결해주는 부분
- 웹 어플리케이션이 시작되고 종료되는 시점에 Servlet Context가 생성하는 이벤트를 연결
- Servlet WebApplicationContext 에서는 Root WebApplicationContext 를 참조 가능하지만, 그 반대로 참조 불가


<br><br><br>



서블릿 컨테이너(Apache Tomcat)가 시작되면, 해당 서블릿 컨테이너는 모든 웹 어플리케이션들을 배포(deploy)하고 로드(load)하면서 서블릿 컨테이너는 ServletContext를 한번 생성하여, 서버의 메모리에 올라가는데

이러한 web.xml에서 설정은 Spring WebApplicationContext를 구성 하는 작업이다. 그리고 Servlet WebApplicationContext(DispatcherServlet)를 등록 해서 `스프링을 서블릿 컨테이너(Apache Tomcat)에서 동작하도록 하는 것`이다.

<br><br>


# 스프링부트와 Servlet 3.0 컨테이너

그러면 서블릿이 내장 된 스프링부트에서는 어떤 형태일까. 스프링에서는 web.xml로 Spring WebApplicationContext를 설정하는 작업을 했지만 스프링부트에서는 web.xml 파일이 없다. 대신 Servlet3.0부터 자반 빈즈 형태인 프로그래밍 방식으로 Context 설정이 가능해 war형태로 배포가 가능하다.

스프링부트에서는 `spring-boot-starter` 모듈에 이미 모든 내장 컨테이너들의 설정을 지원한다. 스프링 부트는 서블릿 컨테이너의 라이프 사이클에 연결하는 대신 스프링 설정을 사용하여 스프링부트 자체와 내장 된 서블릿 컨테이너를 구동시킨다. 필터 및 서블릿 선언은 Spring 구성으로 서블릿 컨테이너에 등록한다.

그래서 아래는 스프링 ServletContainerInitializer 인터페이스를 상속 구현하는 빈을 등록해야하며, 위임 받은 WebApplicationInitializer 인터페이스를 상속 받아 설정한 프로그래밍이다.

내장 컨테이너를 사용하는 스프링부트에서 `@WebServlet, @WebFilter 및 @WebListener 애너테이션이 선언 된 클래스를 자동 등록하여 @ServletComponentScan로 활성화` 시켜 사용한다.

<br>

> WebApplicationInitializer 이용한 프로그래밍 방식

```java
import org.springframework.web.WebApplicationInitializer;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        XmlWebApplicationContext appContext = new XmlWebApplicationContext();
        appContext.setConfigLocation("/WEB-INF/app1-context.xml");

        ServletRegistration.Dynamic registration = container.addServlet("app1", new DispatcherServlet(appContext));
        registration.setLoadOnStartup(1);
        registration.addMapping("/app1/*");
    }
}
```


<br><br>

---
[문서]

- https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-servlet-context-hierarchy
- https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-embedded-container
- https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/SpringServletContainerInitializer.html
- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/WebApplicationInitializer.html


[참고]

- https://whiteship.tistory.com/1712
- https://galid1.tistory.com/525
- https://www.slipp.net/questions/166
- http://blog.daum.net/_blog/BlogTypeView.do?blogid=0Tqpx&articleno=217&categoryId=0&regdt=20130911110427%20
- 토비의 스프링 3.1 책