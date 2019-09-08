---
layout: post
title:  "[MyBatis] MyBatis 개념 및 구조"
subtitle:   "[Mybatis]"
categories: mybatis
tags: mybatis-posting
comments: true
---

퍼시스턴스 프레임워크인 MyBatis 내부구조와 컴파일시 빈 등록할 때 흐름 그리고 스프링 어플리케이션 내 사용차 요청에 따른 호출 흐름 내용 - [원글 URL](https://terasolunaorg.github.io/guideline/5.2.1.RELEASE/en/ArchitectureInDetail/DataAccessDetail/DataAccessMyBatis3.html#overview)

<br>


# 1. MyBatis란?

객체 지향 언어의 자바의 `JDBC를 이용한 퍼시스턴스 프레임워크`이다.
- JDBC를 이용한 커넥션 코드 및 변수 등 중복 작업을 대체해준다.
- SQL, 동적 쿼리, 저장 프로시저 그리고 고급 매핑을 지원하는 SQL Mapper이다.
- SQL쿼리들을 따로 XML파일로 작성하여 프로그램 코드와 SQL문을 코드관리 용이하다.

<br><br>


# 2. MyBatis 특징

- MyBatis-Spring 은 MyBatis에서 MyBatis3와 Spring 연동 라이브러리로 제공된다.
- `싱글톤 패턴으로 스프링 빈(bean)으로 등록하여 주입(DI)하여 쉽게 사용이 가능하다.`
- Mybatis Mapper Interface를 통해 DB에 접근
- 객체 프로퍼티로 파라미터와 결과를 객체(DTO, Map 등)로 자동 매핑을 지원하다.
- 스프링 연동 모듈을 제공해주기 때문에 스프링 설정이 간단하다.
- 트랜잭션을 관리해주기 쉽게 설정이 가능하다.

[![mybatis-s1](/assets/img/devlog/201909/mybatis-s1.png)]()

<br><br>


# 3. MyBatis3의 구성 요소 구조

## 3-1. MyBatis3 Framework 모듈 내 구성요소

구성 요소/구성 파일 | 설명
---- | ----
MyBatis configuration file | `MyBatis3의 작업 설정을 설명하는 XML 파일입니다.`<br>데이터베이스의 연결 대상, 매핑 파일의 경로, MyBatis3의 작업 설정 등과 같은 세부 사항을 설명하는 파일이며, MyBatis3의 기본 작업을 변경하거나 확장 할 때 설정한다.
org.apache.ibatis.session.SqlSessionFactoryBuilder | `MyBatis3 구성 파일을 읽고 생성하는 SqlSessionFactory 구성 요소입니다.`
org.apache.ibatis.session.SqlSessionFactory | `SqlSession을 생성하는 구성 요소입니다.`
org.apache.ibatis.session.SqlSession | `SQL 실행 및 트랜잭션 제어를 위한 API를 제공하는 구성 요소입니다.` MyBatis3를 사용하여 데이터베이스에 액세스할 때 가장 중요한 역할을 하는 구성 요소입니다.
Mapper interface | typeafe에서 `매핑 파일에 정의된 SQL을 호출하는 인터페이스`입니다.<br>MyBatis3는 매퍼 인터페이스에 대한 구현 클래스를 자동으로 생성하므로 개발자는 인터페이스만 생성하면 됩니다.
Mapping file | SQL 및 O/R 매핑 설정을 설명하는 XML 파일입니다.

<br><br>


## 3-2. MyBatis3의 주요 구성 요소가 데이터베이스에 액세스하는 흐름

[![mybatis-s2](/assets/img/devlog/201909/mybatis-s2.png)]()


> 응용 프로그램 시작시 수행되는 프로세스 흐름

`! 어플리케이션이 컴파일 시 스프링에서 MyBatis의 빈 생성 흐름`

1. 응용 프로그램은 SqlSessionFactoryBuilder에 대한 `SqlSessionFactory 빌드를 요청`합니다.
2. SqlSessionFactoryBuilder는 `SqlSessionFactory 생성을 위한 MyBatis 구성 파일을 읽습니다.`
3. SqlSessionFactoryBuilder는 `MyBatis 구성 파일 설정 기반으로 SqlSessionFactory를 생성`되었습니다.

<br>

> 클라이언트의 각 요청에 대해 수행되는 프로세스 흐름

`! 어플리케이션 내에서 개발자가 구현한 사용자의 데이터 요청 흐름`

4. 클라이언트는 응용 `프로그램에 대한 프로세스를 요청`합니다.
5. 응용 프로그램은 SqlSessionFactoryBuilder를 사용하여 작성된 SqlSessionFactory에서 `SqlSession을 가져`옵니다.
6. SqlSessionFactory는 SqlSession을 생성하여 이를 애플리케이션으로 리턴합니다.
7. 응용 프로그램은 `SqlSession에서 Mapper Interface의 구현 객체를 가져옵니다.`
8. 응용 프로그램은 `Mapper Interface 메서드를 호출`합니다.
9. Mapper Interface의 구현 객체는 `SqlSession 메서드를 호출하고 SQL 실행을 요청`합니다.
10. `SqlSession은 매핑 파일에서 실행할 SQL을 가져 와서 SQL을 실행`합니다.

<br><br>


# 4. MyBatis-Spring 연동

## 4-1. MyBatis-Spring의 구성요소

구성 요소/구성 파일 | 설명
---- | ----
org.mybatis.spring.SqlSessionFactoryBean | MyBatis3 SqlSessionFactory 에서 MyBatis 구성 파일에 정의 된 정보를 기반으로 `SqlSessionFactory Spring DI 컨테이너에 객체를 빌드 하고 저장 하는 컴포넌트`
org.mybatis.spring.mapper.MapperFactoryBean | `Singleton Mapper 객체를 빌드하고 Spring DI 컨테이너에 객체를 저장하는 컴포넌트`
org.mybatis.spring.SqlSessionTemplate | `SqlSessionSqlSession 인터페이스를 구현하는 싱글톤 구성 요소`

> SqlSession MyBatis-Spring 구성 요소에 의해 생성 된 오브젝트는 스레드 안전 SqlSession 오브젝트를 생성 할 수 있다. 스프링 빈 생성으로 싱글톤으로 주입(DI)하여 사용할 수 있다.

<br><br>


## 4-2. MyBatis-Spring의 주요 구성 요소가 데이터베이스에 액세스하는 흐름

[![mybatis-s3](/assets/img/devlog/201909/mybatis-s3.png)]()

> 응용 프로그램 시작시 수행되는 프로세스 흐름

`! 어플리케이션이 컴파일 시 스프링에서 MyBatis의 빈 생성 흐름`

1. SqlSessionFactoryBean은 `SqlSessionFactoryBuilder에 대한 SqlSessionFactory 빌드를 요청`합니다.
2. SqlSessionFactoryBuilder는 `SqlSessionFactory 생성을 위한 MyBatis 구성 파일을 읽습니다.`
3. SqlSessionFactoryBuilder는 MyBatis 구성 파일의 정의에 따라 SqlSessionFactory를 생성합니다.<br>이렇게 생성 된 `SqlSessionFactory는 Spring DI 컨테이너에 의해 저장`됩니다.
4. `MapperFactoryBean은 스레드 안전 SqlSession (SqlSessionTemplate)과 스레드 안전 Mapper 객체를 생성`합니다. 이렇게 생성 된 매퍼 객체는 Spring DI 컨테이너에 저장되고 DI는 서비스 클래스 등에 적용됩니다. 매퍼 객체는 스레드 안전 SqlSession (SqlSessionTemplate)을 사용하여 스레드 안전 구현을 제공합니다.

<br>

> 클라이언트의 각 요청에 대해 수행되는 프로세스 흐름

`! 어플리케이션 내에서 개발자가 구현한 사용자의 데이터 요청 흐름`

5. 클라이언트는 응용 프로그램에 대한 `프로세스를 요청`합니다.
6. Application (Service)은 DI 컨테이너에 의해 주입 된 `Mapper Interface 구현체의 메서드를 호출`합니다.
7. Mapper 개체는 호출 된 `메서드에 해당하는 SqlSession (SqlSessionTemplate) 메서드를 호출`합니다.
8. SqlSession (SqlSessionTemplate)은 프록시 사용 및 스레드 안전 SqlSession 메서드를 호출합니다.
9. 프록시 사용 및 스레드 안전 `SqlSession은 트랜잭션에 할당 된 MyBatis3 표준 SqlSession을 사용`합니다.<br>트랜잭션에 할당 된 SqlSession이 없으면 표준 SqlSessionFactory 메소드가 호출됩니다.
10. `SqlSessionFactory는 MyBatis3 표준 SqlSession을 반환`합니다. 반환 된 SqlSession은 트랜잭션에 할당 되었으므로 동일한 트랜잭션 내에 있으면 새 트랜잭션을 만들지 않고 동일한 SqlSession이 사용(공유)됩니다.
11. MyBatis3 표준 `SqlSession은 매핑 파일에서 실행할 SQL을 가져 와서 SQL을 실행`합니다.

<br><br>

---
참고
- https://terasolunaorg.github.io/guideline/5.2.1.RELEASE/en/ArchitectureInDetail/DataAccessDetail/DataAccessMyBatis3.html
- http://www.mybatis.org/spring/ko/sqlsession.html#
- [JSP 커넥션 풀](https://devbox.tistory.com/entry/JSP-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80-1)
