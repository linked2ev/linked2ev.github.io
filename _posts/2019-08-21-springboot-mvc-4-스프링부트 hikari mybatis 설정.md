---
layout: post
title:  "4. 스프링부트 hikari + mybatis 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

작성 중...


<br>

---

> 개발 환경  

- OS: Window 10
- Dev Tool: Eclipse IDE 2019-06
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.7
- Build Tool: Gradle v3
- DB: Mariadb
	+ DBCP: hikariCP
	+ SQL Mapper: MyBatis

---

> 참고 포스팅

- [커넥션 풀이란?](https://linked2ev.github.io/spring/2019/08/14/Spring-3-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80%EC%9D%B4%EB%9E%80/)

---

<br><br>


# 1. Mybatis + hikariCP 의존성 추가

- Mybatis기반으로 DBCP를 hikariCP를 진행하기 때문에 우선 `build.gradle`에서 아래와 같이 의존성을 추가한다.
- 스프링부트 2.0 이상부터 기본적으로 커넥션 풀로 HikariCP를 지원하며 퍼시스턴스 프레임워크에 따라 dependencie를 추가한다.
- Gradle도 의존성을 추가한 다음에 Maven Update처럼 해줘야 하므로 `프로젝트 우클릭 > Gradle > Reflash Gradle Project`
- 다음으로 build path을 다시 잡아줘야 한다. [[참고]](https://linked2ev.github.io/gitlog/2019/08/19/springboot-mvc-3-springboot-gradle-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1/)

<br>

> 추가 부분

```gradle
dependencies{... 
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.0'
    runtimeOnly 'mysql:mysql-connector-java'
}
```

> build.gradle

```gradle
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.0'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	runtimeOnly 'mysql:mysql-connector-java'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

<br>


# 2. DB Datasource 정보 

- 스프링 웹 프로젝트와 비슷하다. DataSource(데이터베이스 연결정보)에 접근 정보를 설정파일에 설정해 놓고 사용한다.


> application.properties

```properties
spring.datasource.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.hikari.jdbc-url={url}
spring.datasource.hikari.username={username}
spring.datasource.hikari.password={password}
```

<br>


# 3. DB 접속 설정

### 스프링 웹 프로젝트 설정
---

- 일반적으로(?) 스프링 웹 프로젝트에서 Apache에서 제공하는 Commons DBCP를 이용한 DB접속을 위한 xml파일로 스프링 설정이다.

<br>


> 예) context-datasource.xml

```xml
<!-- Apache commons DBCP -->
<bean id="dataSource-mysql" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
    <property name="driverClassName" value="${Globals.DriverClassName}"/>
    <property name="url" value="${Globals.Url}" />
    <property name="username" value="${Globals.UserName}"/>
    <property name="password" value="${Globals.Password}"/>
</bean>
```

---

<br><br>

하여튼 계속 포스팅을 진행하면 아래와 같이 자바로 빈을 등록해서 스프링 설정한다.<br>본인은 neo.config 패키지에 DatabaseConfiguration.java라는 클래스를 생성했다.

<br>

> DatabaseConfiguration.java

```java
@Configuration
@PropertySource("classpath:/application.properties")
public class DatabaseConfiguration {
	
	@Bean
	@ConfigurationProperties(prefix="spring.datasource.hikari")
	public HikariConfig hikariConfig() {
		return new HikariConfig();
	}
	
	@Bean
	public DataSource dataSource() throws Exception {
		DataSource dataSource = new HikariDataSource(hikariConfig());
		System.out.println(dataSource.toString());
		return dataSource;
	}
	
}
```


> @Configuration

- @Configuration 애너테이션이 선언 된 java 클래스는 스프링 IoC Container에게 해당 파일이 환경 설정과 관련된 파일(Bean 구성 Class)이라는 것을 인식시킨다.
- `스프링에서 @Configuration이 선언 된 클래스에서 @Bean으로 빈으로 만들었기 때문에 런타임시 스프링에서 싱글톤으로 관리`한다.

<br>

> @Bean

- `스프링 IoC Container에 의해서 만들어진 자바 객체를 스프링 Bean(빈)이라고 부른다.` 즉, 자바 객체이다.
- 빈은 스프링 IoC Container에 의해서 인스턴스화되서 관리되고 일반적으로 싱글톤이다.

<br>

> @PropertySource

- 스프링 IoC Container에서 런타임시 properties값을 가져오기 위함

<br><br>


## 4. DB접속 확인

- 어플리케이션을 구동하면 아래와 같은 콘솔을 볼 수 있다면 정상이다.

[![sts-mvc-4-01](/assets/img/devlog/201908/sts-mvc-4-01.png)]()


<br>


# MyBatis 설정

- MyBatis는 데이터의 저장, 조회, 변경, 삭제를 다루는 클래스 및 설정 파일들의 집합의 `퍼시스턴스 프레임워크(Persistence Framework)`이다.  
- SQL 문장으로 직접 데이터베이스 데이터를 다루는 `SQL Mapper의 한 종류`이다.

> 마이바티스에서는 `SqlSession`를 생성하기 위해 `SqlSessionFactory`를 사용한다. `세션을 한번 생성하면 매핑구문을 실행하거나 커밋 또는 롤백을 하기 위해 세션을 사용할수 있다.` 마지막으로 더 이상 필요하지 않은 상태가 되면 세션을 닫는다
<br><br>`SqlSessionTemplate`은 마이바티스 스프링 연동모듈의 핵심이다. `SqlSessionTemplate`은 `SqlSession`을 구현하고 코드에서 `SqlSession`를 대체하는 역할을 한다. `SqlSessionTemplate` 은 쓰레드에 안전하고 여러개의 DAO나 매퍼에서 공유할수 있다.<br><br>
[참고] http://www.mybatis.org/spring/ko/sqlsession.html

<br>

- MariaDB(DB) 접속 설정은 끝났으니 MyBatis를 연동하기 위해 아래와 같이 코드를 추가해준다.

<br>

> DatabaseConfiguration.java

```java
@Configuration
@PropertySource("classpath:/application.properties")
public class DatabaseConfiguration {
	
	@Autowired
	private ApplicationContext applicationContext;
	
	@Bean
	@ConfigurationProperties(prefix="spring.datasource.hikari")
	public HikariConfig hikariConfig() {
		return new HikariConfig();
	}
	
	@Bean
	public DataSource dataSource() throws Exception {
		DataSource dataSource = new HikariDataSource(hikariConfig());
		return dataSource;
	}
	
	@Bean
	public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
		SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
		sqlSessionFactoryBean.setDataSource(dataSource);
		sqlSessionFactoryBean.setMapperLocations(applicationContext.getResources("classpath:/mapper/**/*.xml"));
		return sqlSessionFactoryBean.getObject();
	}
	
	@Bean
	public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
		return new SqlSessionTemplate(sqlSessionFactory);
	}
}
```

<br>

> @Autowired 

- 스프링 IoC Container를 사용하기 위한 applicationContext 주입

<br>

> @Bean SqlSessionFactory

- DataSource 객체를 받아서 만들어진 setDataSource로 설정 값, setMapperLocations로 mapper 파일 스캔 경로 등 기본을 설정 한 sqlSessionFactory빈이다.
- "classpath:/mapper/**/*.xml"는 예를 들어서 mapper 경로를 src/main/resources/mapper 밑으로 /example/example.xml 파일들를 스캔해서 설정하겠다는 의미이다.

<br>

> @Bean SqlSessionTemplate

- MyBatis에서 SqlSession를 이용해 DataSource(데이터베이스 연결정보)로 실제로 DB에 접근하는 빈이다.

<br><br>

그러면 해당 포스팅에서는 이와 같이 파일 생성 및 프로젝트 구조가 되어있다.

[![sts-mvc-4-02](/assets/img/devlog/201908/sts-mvc-4-02.png)]()

<br>


이렇게 해당 포스팅은 여기까지이고 뒤에 이어서 스프링 MVC 패턴을 이용해서 실제로 Mybatis로 mapper방식과 dao방식으로 포스팅을 진행하겠습니다.

<br><br>

---
참고
- https://charlie-choi.tistory.com/203
- https://taetaetae.github.io/2019/04/21/spring-boot-mybatis-mysql-xml/
- http://www.mybatis.org/spring/ko/sqlsession.html