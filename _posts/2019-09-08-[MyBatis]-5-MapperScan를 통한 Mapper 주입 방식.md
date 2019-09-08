---
layout: post
title:  "[MyBatis] MapperScan를 통한 Mapper 주입 방식"
subtitle:   "[Mybatis]"
categories: mybatis
tags: mybatis-posting
comments: true
---

MyBatis에서 제공하는 mybatis-spring 연동모듈을 이용하면, `@Mapper(애너테이션)을 이용하여 Mapper Interface를 스프링 빈으로 주입받아` DB에 접근하는 방법

<br>


# Mybatis Mapper 방식

- 데이터 접근 객체인 DAO를 생성하지 않음
    +  직접 SqlSessionDaoSupport 나 SqlSessionTemplate 를 사용
    + 마이바티스 스프링 연동모듈은 다른 빈에 직접 주입할 수 있는 쓰레드에 안전한 매퍼를 생성할 수 있다.

<br><br>


# Mapper Scan

```java
@Mapper
public interface BoardMapper {
	
	List<BoardDto> selectBoardList(BoardDto boardDto) throws Exception;

	void insertBoard(BoardDto boardDto) throws Exception;
}
```

- SqlSession이나 마이바티스 객체가 보이지 않는다. 게다가 세션을 생성하거나 열고 닫을 필요도 없어보인다. 마이바티스 스프링 연동모듈이 알아서 처리할 것이다.
- 마이바티스 스프링 연동모듈의 자동스캔기능을 사용할 수 있다. 
자동스캔을 사용하는데는 3가지 방법이 있다. 
    + ```<mybatis:scan/>``` 엘리먼트 사용
    + @MapperScan 애노테이션 사용
    + 스프링 XML파일을 사용해서 MapperScannerConfigurer를 등록
- MapperScan 방식을 통해 특정 인터페이스에 @Mapper 애노테이션이 지정되어 있다면 스프링에서 MapperProxy로 등록
- `@Mapper 인터페이스 또한 Mybatis의 SqlSession(SqlSessionTemplate)을 이용하여 Sql Mapping 매핑 처리`

<br><br>


# Mapper Interface

> Mapper Interface를 통해 mapper xml이용한 방법

패키지 전체 경로가 mapper xml의 mapper xml 상의 namespace이고 인터페이스 메소드가 query id로 호출되는 방식이다.

```java
@Mapper
public interface BoardMapper {
	
	List<BoardDto> selectBoardList(BoardDto boardDto) throws Exception;
	
	void insertBoard(BoardDto boardDto) throws Exception;
}
```

> Mapper Interface 내에 @애너테이션을 이용한 방법

mapper xml 없이 Mapper 인터페이스 상에 @Select, @Insert, @Update, @Delete 등의 애너테이션을 선언해 query를 작성해서 하는 방법

```java
@Mapper
public interface BoardMapper  {

    @Select("select title, conents from t_board")
    List<BoardDto> selectBoardList(BoardDto boardDto) throws Exception;
	
    @Insert("insert into t_board (title, contents) values (#{title}, #{conents})")
	void insertBoard(BoardDto boardDto) throws Exception;
 
}
```

<br><br>

> @Mapper 애너테이션을 통해 개발 방법이 DAO객체를 굳이 생성하지 않아 개발에 편한 방법이기도 하지만, 도메인 주도 개발 프로젝트가 아니라면 DAO객체를 공통 DAO로 작업 후에 개발하는 방법도 좋은 방법이다.


<br><br>

---
참고
- http://www.mybatis.org/spring/ko/mappers.html
