---
layout: post
title:  "10. 스프링부트 MVC - MyBatis DAO 방식"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

해당 포스팅은 `@Repository 애너테이션을 사용해서 DAO 객체를 생성해 MyBatis의 SqlSession을 사용하는 방식이다.`

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
- #Lombok #Log4j2 #DAO

---

> 참고 URL

- [[MyBatis] DAO와 SqlSessionTemplate, SqlSessionDaoSupport](https://linked2ev.github.io/mybatis/2019/09/08/MyBatis-4-DAO%EC%99%80-SqlSessionTemplate,-SqlSessionDaoSupport/)
- [[MyBatis] MapperScan를 통한 Mapper 주입 방식](https://linked2ev.github.io/mybatis/2019/09/08/MyBatis-5-MapperScan%EB%A5%BC-%ED%86%B5%ED%95%9C-Mapper-%EC%A3%BC%EC%9E%85-%EB%B0%A9%EC%8B%9D/)

이정도는 보고 가면 좋을 듯 해서 복붙 수준의 포스팅 내용이다.

---

> 오랜만에 다시 스프링부트 포스팅이다. 관심사와 실제 포스팅이 진행상태가 달라서 이제는 숙제가 된 느낌이다...


<br><br>


# @Repository, DAO 객체 생성
 
DAO 객체를 생성하고, @Repository 애너테이션 선언해 빈으로 등록하면서 Data Access Layer라는 것을 스프링이 인지하게 된다.

마이바티스 스프링 모듈의 SqlSessionTemplate을 을 주입 받아서 Mapper xml 파일의 namespace 와 queryid 로 쿼리를 호출해 결과를 리턴 받는 개념이다.

<br>

> BoardDao.java

- neo.apps.board.dao

```java
@Repository
public class BoardDao {
	
	@Autowired
	private SqlSessionTemplate sqlSession;
	
	public int selectBoardListCnt(BoardDto boardDto){
		return sqlSession.selectOne("Board.selectBoardListCnt", boardDto);
	}
	
	public List<BoardDto> selectBoardList(BoardDto boardDto){
		return sqlSession.selectList("Board.selectBoardList", boardDto);
	}
	
	public BoardDto selectBoard(int boardIdx){
		return sqlSession.selectOne("Board.selectBoard", boardIdx);
	}
	
	public int insertBoard(BoardDto boardDto){
		return sqlSession.insert("Board.insertBoard", boardDto);
	}
	
	public int updateBoard(BoardDto boardDto){
		return sqlSession.update("Board.updateBoard", boardDto);
	}
	
}
```

<br><br>


# ServiceImpl, DAO 주입

구현체에서 기존에 Mapper Interface가 주입 방식이 아닌 DAO 객체를 주입받아서 쿼리에 접근할 수 있게 아래와 같이 수정했다.

주석은 삭제해도 상관없을 듯 하다.

```java
@Service
public class BoardServiceImpl implements BoardService {

	//@Autowired
	//private BoardMapper boardMapper;
	
	@Autowired
	private BoardDao boardDao;
	
	/*
	MyBatis Mapper 방식
	public int selectBoardListCnt(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardMapper.selectBoardListCnt(boardDto);
	}

	public List<BoardDto> selectBoardList(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardMapper.selectBoardList(boardDto);
	}
	
	public BoardDto selectBoard(HttpServletRequest request, int boardIdx) throws Exception {
		return boardMapper.selectBoard(boardIdx);
	}
	
	public void insertBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
		boardMapper.insertBoard(boardDto);
	}
	
	public void updateBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
		boardMapper.updateBoard(boardDto);
	}
	*/
	
	public int selectBoardListCnt(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardDao.selectBoardListCnt(boardDto);
	}
	
	public List<BoardDto> selectBoardList(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardDao.selectBoardList(boardDto);
	}
	
	public BoardDto selectBoard(HttpServletRequest request, int boardIdx) throws Exception {
		return boardDao.selectBoard(boardIdx);
	}
	
	public int insertBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardDao.insertBoard(boardDto);
	}
	
	public int updateBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return boardDao.updateBoard(boardDto);
	}
}
```


<br><br>


# SQL.xml namespace 변경

DAO로 접근 시에는 namespace를 전체경로로 적지 않아도 되기에 아래와 같이 수정한다.

```xml
<!-- <mapper namespace="neo.apps.board.mapper.BoardMapper"> -->
<mapper namespace="Board">
```

<br><br>

해당 포스팅은 이렇게 끝이다. 이어서 DAO를 공통 DAO로 생성 후 접근하는 방식에 대해서 알아보겠습니다.