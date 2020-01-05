---
layout: post
title:  "4. springboot restful 방식으로 게시판 변경"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-restful
comments: true
---

restful 방식으로 게시판으로 변경

<br><br>


> 개발 환경 및 내용

- OS: Window 10
- Dev Tool: Intellij - 2019.2.3
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.8
- Build Tool: Gradle v3
- RDBMS: MariaDB


---

> 관련 URL

- [RESTful과 REST API](https://linked2ev.github.io/devlog/2019/10/09/WEB-RESTful-and-REST-API)
- [RESTful 디자인]( https://linked2ev.github.io/devlog/2019/12/28/WEB-RESTful-design)
- [해당 샘플 프로젝트 github 주소](https://github.com/linked2ev/neo)

---

<br><br>


# REST란?

REST는 웹의 장점을 살려 HTTP프로토콜 기반으로 의도에 맞게 필요한 웹의 고유한 자원(URI)에 접근하는 방식인 하이퍼미디어 기반 `분산 시스템을 설계하기 위한 아키텍처 스타일`이다.

> REST 구성 요소

- HTTP URI : 자원(Resource)
- HTTP Method : 행위(Verb)
- MIME Type : 표현 방식(Representation of Resource)

리소스 중심으로 URI를 설계하며, 해당 자원에 기능에 따라 HTTP메서드인 GET, POST, PUT, DELETE, HEAD, OPTIONS 로 정의해서 RESTful하게 설계한 API서버를  REST API라고 말한다.

<br>


기존의 스프링에서 ModelAndView을 통한 REST 웹 서비스 방식 개발은 환경 구성이 무겁기 때문에 스프링4이상 부터 @RestController와 @ResponseStatus를 지원하면서 HttpMessageConverter와 어노테이션 기반으로 REST 웹 서비스 개발이 더욱 쉽고 경량화 되었다.

@RestController는 @Controller + @ResponseBody으로써 @ResponseBody를 어노테이션을 추가 할 필요가 없으며, 모든 응답은 HTTP ResponseBody 에 직접 쓰여진다.

클라이언트는 REST API 설계 된 `리소스`에 대해서 요청을 한다. 그러면 Spring MVC 컨트롤러에서 HTTP메소드인 GET, POST, PUT, DELETE `행위` 등을 처리 해서 HttpMessgeConverter을 사용하여 view의 렌더링을 할 필요 없이 클라이언트가 요청한 자원에 원하는 형식(JSON, XML, HTML 등)으로 `표현` 할 수 있다.

<br><br>


## 스프링 @Controller - ModelAndView

일반적인 모놀리식(Monolithic) 아키텍처 프로젝트는 화면과 서버가 같은 프로젝트에서 관리하는 스프링 MVC는 아마도 아래와 같은 방식일 것이다.

<br>

```java
@Slf4j
@Controller
@RequestMapping("/board")
public class BoardController {

	@Autowired
	private BoardService boardService;

    //목록
	@RequestMapping("getBoardList")
	public ModelAndView noticeList(HttpServletRequest request, BoardDto boardDto) throws Exception {
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
	
    //상세
	@RequestMapping("getBoardDetail")
	public ModelAndView noticeDetail(HttpServletRequest request, @RequestParam Integer boardIdx) throws Exception {
		ModelAndView mv = new ModelAndView();
		
		// 상세
		BoardDto detail = this.boardService.selectBoard(request, boardIdx);
		mv.addObject("detail", detail);
		
		return mv;
	}
	
    //등록 폼
	@RequestMapping("form")
	public ModelAndView noticeForm(HttpServletRequest request, @RequestParam Integer boardIdx) throws Exception {
		ModelAndView mv = new ModelAndView();
		BoardDto detail = new BoardDto();
		
		if (boardIdx != null) {
			// 상세
			detail = this.boardService.selectBoard(request, boardIdx);
		}
		mv.addObject("detail", detail);
		
		return mv;
	}
	
    //등록
	@RequestMapping(value="insertBoard")
	@ResponseBody
	public String noticeFormProc(HttpServletRequest request, @RequestBody BoardDto boardDto) throws Exception {
		JSONObject rData = new JSONObject();
    
        this.boardService.insertBoard(request, boardDto);
        rData.put("rMsg", "정상적으로 등록되었습니다.");
    
		return rData.toString();
	}
	
    //수정
	@RequestMapping(value="updateBoard")
	@ResponseBody
	public String noticeFormProc(HttpServletRequest request, @RequestBody BoardDto boardDto) throws Exception {
		JSONObject rData = new JSONObject();
		
		if (boardDto.getBoardIdx() != null) {
		    this.boardService.updateBoard(request, boardDto);
			rData.put("rMsg", "정상적으로 수정되었습니다.");
		} else {
            rData.put("rMsg", "필수 값이 없습니다.");
		}
		
		return rData.toString();
	}

    //삭제
	@RequestMapping("deleteBoard")
    @ResponseBody
	public void boardDelete(HttpServletRequest request, @RequestParam Integer boardIdx) throws Exception {
		this.boardService.deleteBoard(request, boardIdx);
	}
}
```

<br><br>


## 스프링 @RestController - REST

@RestController에서 지원하는 @ResponseBody를 이용해서 return하는 방식이다.

<br>

> HTTP 메서드

- http에서는 여러가지 메서드가 있는데 POST, GET, PUT, DELETE 이 4가지의 Method를 가지고 CRUD를 할 수 있다. 아래는 해당 URI로 요청시 데이터를 처리하는 기능이다.

메서드 | 의미 | 역할
---- | ---- | ----
POST | Create | 해당 URI 리소스를 생성
GET	| Read | 해당 URI 리소스를 조회
PUT | Update | 해당 URI 리소스를 수정
DELETE | Delete | 해당 URI 리소스를 삭제


<br>

```java
@Slf4j
@RestController
@RequestMapping("/v1/api")
public class BoardController {

    @Autowired
    private BoardService boardService;

    @GetMapping("board")
    public List<Board> boardList(HttpServletRequest request, @ModelAttribute Board board) throws Exception {
        return this.boardService.selectBoardList(request, board);
    }

    @GetMapping("board/{boardIdx}")
    public Board detailBoard(HttpServletRequest request, @PathVariable(name="boardIdx", required=true) int boardIdx) throws Exception {
        return this.boardService.selectBoard(request, boardIdx);
    }

    @PostMapping("board")
    public void insertBoard(HttpServletRequest request, @RequestBody Board board) throws Exception {
        this.boardService.insertBoard(request, board);
    }

    @PutMapping("board/{boardIdx}")
    public void updateBoard(HttpServletRequest request, @PathVariable(name="boardIdx", required=true) int boardIdx, @RequestBody Board board) throws Exception {
        board.setBoardIdx(boardIdx);
        this.boardService.updateBoard(request, board);
    }

    @DeleteMapping("board/{boardIdx}")
    public void deleteBoard(HttpServletRequest request, @PathVariable(name="boardIdx", required=true) int boardIdx) throws Exception {
        this.boardService.deleteBoard(request, boardIdx);
    }
}
```

위에 두 Controller의 Mapping URI의 비교이다.

기능 | 메서드 | restful URI | 기존 URI
---- | ---- | ---- | ----
게시판 등록 | POST | /board | /board/insertBoard
게시판 목록 | GET | /board | /board/getBoardList 
게시판 상세 | GET | /board/{boardIdx} | /board/getBoardDetail
게시판 수정 | PUT | /board/{boardIdx} | /board/updateBoard
게시판 삭제 | DELETE | /board/{boardIdx} | /board/deleteBoard

<br>

우선 기존 URI같은 경우는 URI내에 board가 중첩되거나 get~, insert~, ~list, update~, delete~ 등 동사를 의미하는 표현이 들어가며 만약 SI 프로젝트를 실제로 진행 할 경우는 각 개발자들마다 걸어온(?) 환경이 다르고 개발리더의 개발적이 지침이 없다면 동사표현이 정말로 상이하다. 그래서 select~, red~, mod~, del~ 등 다소 URI가 복잡해보인다.

<br>

RESTful URI 같은 경우는 리소스의 자원을 표현하면서 기능에 따라 메서드를 통해 설계하기 때문에 기존 URI보다 간결해져서 의미 파악이 직관적이고 가독성이 좋다. 그리고 스프링에서 @PathVariable 등 여러 어노테이션을 지원하기에 개발시에도 편리하다.


<br><br>

다음 포스팅은 스프링 rest방식에서 ResponseEntity<T>에 관련해서 진행하겠습니다.


---
[참고]