---
layout: post
title:  "11. 스프링부트 MVC - MyBatis 공통 DAO"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

해당 포스팅은 `각 도메인마다 DAO 생성이 아닌 공통 DAO 하나만 생성 후 queryId와 parameter를 이용해 처리하는 방식이다.`

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

<br><br>

> 개인적인 생각은 포스팅 마지막에 두고, 해당 포스팅에 `빨간색 글씨 키워드`에 대해서 잘 모르면 개인적으로 구글링 하시라고 용어로 적어놨다.

<br>

# 공통 DAO 객체 생성
 
각 도메인마다 DAO 생성이 아닌 따로 어플리케이션 내에 `공통 DAO 생성 후 queryId와 parameter를 보내서 데이터 결과 Object를 return`을 해주는 개념이다.

<br>

## @Repository("name")
---

```java
@Repository("cmDao")
```

`@Repository에 name를 지정`한건 큰 의미를 둬서 코딩한건 아니다. name를 지정 후 주입 할 때 @Autowired가 아닌 @Resource("name") 으로 하는 방법도 있다는 것을 혹시나 모르면 인지시켜주기 위함이다.

간단하게 @Autowired는 스프링 진영, @Resource는 자바 진영이다 생가하고 해당 포스팅에서는 패스하면 좋다.

<br>


## 리턴 타입 제네릭 설정
---

자바에는 `제네릭`이라는게 있고, `<?>, <T>, <E>, <Object> 등` 선언해서 class, object 등을 타입을 설정할 수 있다.

그리고 `오버로딩` 개념을 활용해서 매개변수의 유형과 개수를 다르게 하여 Object, Map 등 parameter에 따라 메서드를 추가해서 필요에 따라 호출하고 리턴받으면 된다.

<br>

> CommonDao.java

- neo.com.dao

```java
@Repository("cmDao")
public class CommonDao {
	
	@Autowired
	private SqlSessionTemplate sqlSession;
	
	public int selectListCnt(String queryId, Object parameterObject){
		return sqlSession.selectOne("Board.selectBoardListCnt", parameterObject);
	}
	
	public <E> List<E> selectList(String queryId, Object parameterObject){
		return sqlSession.selectList("Board.selectBoardList", parameterObject);
	}
	
	public <T> T select(String queryId, int parameterInt){
		return sqlSession.selectOne("Board.selectBoard", parameterInt);
	}
	
	public <T> T select(String queryId, Object parameterObject){
		return sqlSession.selectOne("Board.selectBoard", parameterObject);
	}
	
	public int insert(String queryId, Object parameterObject){
		return sqlSession.insert("Board.insertBoard", parameterObject);
	}
	
	public int update(String queryId, Object parameterObject){
		return sqlSession.update("Board.updateBoard", parameterObject);
	}
	
}
```

<br><br>


# ServiceImpl, 공통 DAO 주입

## @Resource(name="name")
---

```java
@Resource(name="cmDao")
private CommonDao cmDao;
```

@Autowired 가 아닌 @Resource로 빈을 주입할 수 있고 이와 같이 name를 지정해서 주입 받을 수 있다.

<br>

```java
@Service
public class BoardServiceImpl implements BoardService {

	@Resource(name="cmDao")
	private CommonDao cmDao;

	public int selectBoardListCnt(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return cmDao.selectListCnt("Board.selectBoardListCnt", boardDto);
	}
	
	public List<BoardDto> selectBoardList(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return cmDao.selectList("Board.selectBoardList", boardDto);
	}
	
	public BoardDto selectBoard(HttpServletRequest request, int boardIdx) throws Exception {
		return cmDao.select("Board.selectBoard", boardIdx);
	}
	
	public int insertBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return cmDao.insert("Board.insertBoard", boardDto);
	}
	
	public int updateBoard(HttpServletRequest request, BoardDto boardDto) throws Exception {
		return cmDao.update("Board.updateBoard", boardDto);
	}
}
```

<br><br>


> `개인적인 생각이다.` MyBatis로 DAO방식일 경우 이렇게 각 도메인마다 DAO객체를 생성하는 수고를 줄일 수 있다.
<br><br>
하지만, 아마도 `스프링부트`로 웹 프로젝트를 생성했을때는 설계 자체부터가 아마 `MSA`기반 또는 `도메인 주도 개발`이거나 작은 기능 어플리케이션을 개발하기에 이와 같은 방법은 오히려 의미가 없다.
<br><br>
뭔가 하나의 스프링 프로젝트에 모든 기능이 있고 `도메인 주도 개발`이 아니고 Model관리가 쉽지 않을 거 같고 Map를 주로 DTO역할로 할 경우 그리고 개발 환경이(불특정 개발자, 회사, 인프라, 프로젝트 작은 규모, 유지보수 등)...일 때는 좋다고 생각된다.
<br><br>
결론은 하나의 좋은 개발 방법이 될 수도 있지만, 그냥 더 좋게 설계하고 시작하자.

<br><br>

추후에는 웹 프로젝트에서 기본적으로 더 필요한 Interceptor, AOP, Transaction 설정에 대해서 마무리 짓고, restful에 대해 그리고.