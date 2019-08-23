---
layout: post
title:  "6. 스프링부트 MVC 게시판 (MyBatis Mapper 사용)"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

스프링부트 MVC 구조로 사용자 화면은 템플릿엔진인 thymeleaf를 사용하고 데이터 계층은 Mybatis의 Mapper Interface으로 SQL Mapper XML파일을 사용하는 내용이다.

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

---

> 관련 포스팅

- [4. 스프링부트 MyBatis + HikariCP + MariaDB 설정](https://linked2ev.github.io/gitlog/2019/08/21/springboot-mvc-4-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MyBatis-+-HikariCP-+-MariaDB-%EC%84%A4%EC%A0%95/)
- [5. 스프링부트 HikariCP MyBatis 옵션 설정](https://linked2ev.github.io/gitlog/2019/08/22/springboot-mvc-5-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-HikariCP-MyBatis-%EC%98%B5%EC%85%98-%EC%84%A4%EC%A0%95/)

> 참고 할 내용

- [Spring에서 말하는 Spring MVC 이란?](https://linked2ev.github.io/spring/2019/08/12/Spring-1-%EC%8A%A4%ED%94%84%EB%A7%81-MVC-%EC%9D%B4%EB%9E%80/)
- [Spring MVC 패턴](https://linked2ev.github.io/spring/2019/08/14/Spring-2-%EC%8A%A4%ED%94%84%EB%A7%81-MVC-%ED%8C%A8%ED%84%B4/)

---

<br><br>


## 스프링 MVC 구조
---

[![sts-mvc-6-01](/assets/img/devlog/201908/sts-mvc-6-01.png)]()

- Mybatis의 Mapper Interface으로 SQL Mapper XML파일을 사용한 구조이다.
- 해당 포스팅에서 파일 추가 작업이 된 후 프로젝트 구조이다.

<br><br>



## DTO(Data Transfer Object) 생성
---

`웹 어플리케이션의 각 계층에 데이터를 교환하는 Model Object인 DTO`생성해줍니다.

<br>

> BoardDto

```java
public class BoardDto {
	
	public int idx;
	
	public String title;
	
	public String content;
	
	public int hitcnt;
	
	public String regid;
	
	public String regdate;

	public int getIdx() {
		return idx;
	}

	public void setIdx(int idx) {
		this.idx = idx;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getContent() {
		return content;
	}

	public void setContent(String content) {
		this.content = content;
	}

	public int getHitcnt() {
		return hitcnt;
	}

	public void setHitcnt(int hitcnt) {
		this.hitcnt = hitcnt;
	}

	public String getRegid() {
		return regid;
	}

	public void setRegid(String regid) {
		this.regid = regid;
	}

	public String getRegdate() {
		return regdate;
	}

	public void setRegdate(String regdate) {
		this.regdate = regdate;
	}

	@Override
	public String toString() {
		return "BoardDto [idx=" + idx + ", title=" + title + ", content=" + content + ", hitcnt=" + hitcnt + ", regid="
				+ regid + ", regdate=" + regdate + "]";
	}
}
```

- 일반적으로 위와 같이 해당 클래스 내에서 이클립스 기능으로 getter()/setter()와 toString()를 개발자의 수고가 없이 필드명을 정의해주면 자동으로 만들어 줍니다.
    + 우클릭 > Source > Generate Getters and Setters...
    + 우클릭 > Source > Generate toString()...
- 필드명이 적기 때문에 그나마 코드가 짧다.(다음 포스팅에서 Lombok으로 변경)

<br><br>


## Presentation Layer인 Controller 생성
---

클라이언트의 요청에 맞는 URL맵핑과 비즈니스 로직을 호출하고 Dispatcher역할을 하는 `Presentation Layer(프레젠테이션 계층)`인 @Controller이 선언 된 컨트롤러 영역 클래스이다.


<br>

> BoardController.java

```java
@Controller
@RequestMapping("/board")
public class BoardController {

	@Autowired
	private BoardService boardService;

	@RequestMapping("list")
	public ModelAndView notice(HttpServletRequest request, BoardDto boardDto) throws Exception {
		ModelAndView mv = new ModelAndView();
		
		// 목록 갯수
		int totalCnt = this.boardService.selectBoardListCnt(request, boardDto);
		
		// 목록
		List<BoardDto> list = null;
		if(totalCnt > 0){
			list = this.boardService.selectBoardList(request, boardDto);
		}
		
		mv.addObject("totalCnt", totalCnt);
		mv.addObject("list", list);
		mv.addObject("boardDto", boardDto);
		
		return mv;
	}
}
```

<br><br>


## Bussiness Logic Layer인 Service 생성
---

실제 사용자의 요구에 맞는 서비스를 담당하고 데이터 계층을 호출하는 `Bussiness Logic Layer(비즈니스 로직 계층)`이며 @Service 애너테이션이 선언 된 실제 비즈니스 로직이 코딩되어 있는 ServiceImpl(구현클래스)와 서비스 Service(인터페이스)이다.

서비스 영역에서는 

> BoardServiceImpl.java

```java
@Service
public class BoardServiceImpl implements BoardService {

	@Autowired
	private BoardMapper boardMapper;
	
	public int selectBoardListCnt(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardMapper.selectBoardListCnt();
	}

	public List<BoardDto> selectBoardList(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardMapper.selectBoardList();
	}
}
```

<br>

> BoardService.java

```java
public interface BoardService {

	public int selectBoardListCnt(HttpServletRequest request, BoardDto boardDto) throws Exception;

	public List<BoardDto> selectBoardList(HttpServletRequest request, BoardDto boardDto) throws Exception;
}
```

<br><br>


## Data Access Layer인 Mapper 생성
---

SI프로젝트에서는 Persistence Framework가 MyBatis일 경우 MyBatis 장점인 비즈니스 로직과 SQL구문을 나누는 방식인 @Repository를 사용한 DAO객체 만들거나 @Mapper을 이용해 Mapper 인터페이스를 만들어 `Data Access Layer(데이터 접근 계층)`을 구현하고 SQL구문이 있는 SQL Mapper 파일의 namespace와 id를 불러오는 방식이다. 

또한 SQL Mapper 작성 없이 Mapper 인터페이스 상에 @Select, @Insert, @Update, @Delete 등의 annotation을 통해 자바로만 객체지향 개발을 할 수 있다.

아래는 Mybatis 스프링 모듈이 @Mapper 애너테이션이 선언 된 클래스들을 스캔하여 Mapper 인터페이스로 인식하며, SQL Mapper인 XML 파일의 namespace(Mapper 인터페이스 전체 경로)와 id(Mapper 인터페이스 메서드)로 맵핑하여 SQL문을 호출하여 결과값을 가진다.

<br>


> BoardMapper.java

```java
@Mapper
public interface BoardMapper {
	
	int selectBoardListCnt() throws Exception;
	
	List<BoardDto> selectBoardList() throws Exception;
	
}
```

> board_SQL.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="neo.apps.board.mapper.BoardMapper">

	<select id="selectBoardListCnt" parameterType="BoardDto" resultType="int">
		SELECT
				COUNT(*) AS TOTCNT
		FROM 
				T_BOARD
	</select>

	<select id="selectBoardList" parameterType="BoardDto" resultType="BoardDto">
		SELECT
				IDX
				, TITLE
				, CONTENT
				, HIT_CNT
				, REG_ID
				, REG_DATE
		FROM
				T_BOARD
	</select>

</mapper>
```

<br>


## Presentation Layer인 View 생성
---

클라이언트의 요청을 처리한 후에 `Dispatcher가 @RequestMapping에 View Name으로 model를 사용자에게 보여주는 단계`이다.  

스프링부트 프로젝트를 만들때 thymeleaf를 의존성이 추가된 것을 볼 수 있고 사용할려면 추가만 하면 된다. jsp와 문법만 상이하고 사용하면 된다.(배움은 필요하다.)

아래는 view 역할을 하는 list.html은 thymeleaf 문법의 일부분이다.

<br>

> build.gradle

```gradle
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

> list.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" xmlns:layout="http://www.ultraq.net.nz/web/thymeleaf/layout">
<head>
<meta charset="UTF-8" />
<title>스프링부트 thymeleaf</title>

<meta name="viewport" content="width=device-width, initial-scale=1"/>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css"/>
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>


<style>
	.devCss {
		 text-align: center;
	}
</style>

</head>
<body>
	
	<div class="container">
		<div class="row col-md-7 table-responsive">
			<table class="table devCss" id="listTable">
		    <thead>
			    <tr>
			         <th>제목</th>
			         <th>내용</th>
			         <th>등록한 ID</th>
			         <th>등록한 날짜</th>
			     </tr>
		     </thead>
		     <tbody>
			     <tr th:if="${totalCnt} > 0" th:each="list : ${list}">
			         <td th:text="${list.title}">title</td>    
			         <td th:text="${list.content}">content</td>
			         <td th:text="${list.regid}">regid</td>
			         <td th:text="${list.regdate}">regdate</td>
			     </tr>	
			     <tr th:unless="${totalCnt} > '0'">
			     	<td colspan="4">게시물 없음</td>
			     </tr>
		     </tbody>
		     </table>
	     </div>
	</div>
	
</body>
</html>
```

<br><br>

> thymeleaf 문법에 관련해서 정리가 잘 되어 다른 분에 블로그다. 가볍게 보시면 도움이 될 듯 하다.
<br>- 참고: https://eblo.tistory.com/55
<br><br>추후에 본인은 thymeleaf대신 handlebar라는 템플릿엔진으로 대체해 개발할 때, 템플릿엔진에 대해서 다시 알아보겠다.

<br><br>

---

[참고]

- https://www.thymeleaf.org/doc/tutorials/2.1/usingthymeleaf.html