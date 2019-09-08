---
layout: post
title:  "[MyBatis] DAO와 SqlSessionTemplate, SqlSessionDaoSupport"
subtitle:   "[Mybatis]"
categories: mybatis
tags: mybatis-posting
comments: true
---

MyBatis3에서 SqlSession 과 SqlSessionTemplate, SqlSessionDaoSupport 개념설명 그리고 mybatis-spring 연동모듈을 사용해서, `@Repository(애너테이션)을 선언해 DAO객체를 생성해서 DB에 접근하는 방법`

<br>


# SqlSession

- MyBatis에서는 SqlSession를 생성하기 위해 SqlSessionFactory를 사용
    + 세션을 한번 생성하면 매핑구문을 실행하거나 커밋 또는 롤백을 하기 위해 세션을 사용할수 있다.
    + 더 이상 필요하지 않은 상태가 되면 세션을 닫는다.

- MyBatis 스프링 연동 모듈을 사용
    + SqlSessionFactory를 직접 사용할 필요가 없다. 왜냐하면, 스프링 트랜잭션 설정에 따라 자동으로 커밋 혹은 롤백을 수행하고 닫혀지는, 쓰레드에 안전한 SqlSession 개체가 스프링 빈에 주입될 수 있기 때문이다.
    + > mybatis-spring 의존성
        ```xml
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
        ```
- Mybatis만 사용하면, SqlSessionFactory는 SqlSessionFactoryBuilder를 사용해서 생성한다. `Mybatis + Spring 연동모듈(라이브러리)에서는 SqlSessionFactoryBean가 대신 사용`된다.

<br><br>


# SqlSessionDaoSupport

> SqlSessionDaoSupport 객체 상속 방식

- 애너테이션으로 sqlSession(SqlSessionFactoryBean)을 수정자(Setter)방식으로 주입해서 super(SqlSessionDaoSupport)의 setSqlSessionFactory 메서드를 호출

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations" value="classpath*:sample/config/mappers/**/*.xml" />
</bean>
```

```java
@Repository
public class SampleDao {

    @Autowired
    public void setSqlSessionFactory(SqlSessionFactory sqlSessionFactory) {
        super.setSqlSessionFactory(sqlSessionFactory)
    }

    public int insert(String queryId, Object parameterObject) {
        return getSqlSession().insert(queryId, parameterObject);
    }
}
```

<br><br>


# SqlSessionTemplate

> SqlSessionTemplate 객체 상속 방식

- SqlSessionTemplate은 마이바티스 스프링 연동모듈의 핵심
    + SqlSessionTemplate은 SqlSession을 구현하고 코드에서 SqlSession를 대체하는 역할을 한다. 
    + SqlSessionTemplate 은 쓰레드에 안전하고 여러개의 DAO나 매퍼에서 공유할수 있다. 
- SqlSessionTemplate 객체 사용
    + SqlSessionTemplate를 bean 으로 등록
    + 클래스에서 SqlSessionTemplate 필드(Field)방식으로 주입하여 사용
    + SqlSession (SqlSessionTemplate)은 프록시 사용 및 스레드 안전 SqlSession 메서드를 호출

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations" value="classpath*:sample/config/mappers/**/*.xml" />
</bean>

<bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg index="0" ref="sqlSession"/>
</bean> 
```

```java
@Repository
public class SampleDao {

    @Autowired
    private SqlSessionTemplate sqlSession;

    public void insert(String queryId, Object parameterObject) {
        sqlSession.insert(queryId, parameterObject);
    }
}
```


<br><br>

---
참고
- http://www.mybatis.org/spring/ko/sqlsession.html#
