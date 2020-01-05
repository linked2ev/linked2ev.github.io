---
layout: post
title:  "5. springboot ResponseEntity 적용"
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



# ResponseEntity 로 변경

Spring4 이상부터 @Controller와 @ResponseBody 을 합쳐놓은 @RestController를 지원하기애 기본적으로 ResponseBody로 리턴하고 있다. 그러면 ResponseEntity는? 일반적으는 하는 역할은 동일하다. 하지만 ResponseEntity로 리턴하는 경우는 본문정보와 헤더 정보, HTTP 상태코드를 좀 더 세밀하게 개발자가 처리하기 수월하면서 HATEOAS, builder패턴 등을 활용해서 개발하기 용이하다.

<br>

> ResponseBody vs ResponseEntity<T\>

```java
@GetMapping(value = "/board/{id}") //@ResponseBody
public Board getBoard(@PathVariable int id) {
    Board board = boardService.selectBoard(id);
    return board;
}

@GetMapping(value = "/board/{id}")
ResponseEntity<Board> getBoard(@PathVariable int id) {
    Board board = boardService.selectBoard(id);
    return new ResponseEntity<Board>(board, HttpStatus.OK);
    //return ResponseEntity.ok().body(board);
}
```

<br>

> HATEOAS(Hypermedia As The Engine Of Application State)

HATEOAS는 독립된 시스템에서 클라이언트서버가 RESTful하게 디자인 된 API서버에 의해 동적으로 상호작용이 가능하다. 쉽게 말하면 클라이언트가 서버에 요청시 서버는 header에 POST, GET, PUT, DELETE 등 URI를 동적으로 설정하고 response를 한다. 

그러면 클라이언트에서 서버에서 받은 URI로 적용해서 호출하다면, API서버에서 URI가 변경되어도 클라이언트는 호출URI를 변경할 필요가 없다. 또한 권한에 따른 URI를 제한을 할 수 있다. 그리고 클라이언트는 본문응답과 URI정보를 통해서 요청한 후에 해당 리소스에 대해 예측가능하게 한다.

왜 예측가능하게 설계를 하면. RESTful은 독립적인 진화이다. 독립된 시스템이기에 클라이언트와 API서버는 독립적으로 개발을 진행되기에 단순히 응답 값을 성공과 실패로 주면 파악하기 쉽지 않기 때문이다. 우선 hateoas 를 맛보기 위해서 hateoas dependency를 추가하고 실제로 POSTMAN으로 API 테스트해 보는게 와닿는다.

<br>

> build.gradle

```gradle
implementation 'org.springframework.boot:spring-boot-starter-hateoas'
```

> @RestController

```java
//등록
@PostMapping
public ResponseEntity boardInsert(@RequestBody Board board) throws Exception {
    Integer idx = this.boardService.insertBoardSelectKey(board);
    return ResponseEntity.created(linkTo(BoardApiController.class).slash(idx).toUri()).build();
}
```

> POSTMAN Headers값

```
location: http://localhost:8080/v2/api/board/36
```

<br>

추후에 JPA 포스팅 할 때 HATEOAS와 Self-Descriptive를 적용하도록 해서 WEB API서버에서 좀 더 RESTful한 API로 갈 수 있게 포스팅하겠습니다.

<br><br>

우선 아래는 RESTful에 한단계 다가간 ResponseEntity과 HTTP 상태코드 활용에 포커스이다.

> BoardApiController.java

```java
@Slf4j
@RestController
@RequestMapping("/v2/api/board")
public class BoardApiController {

    @Autowired
    private BoardService boardService;

    //목록
    @GetMapping
    public ResponseEntity<List<Board>> boardList(HttpServletRequest request, @ModelAttribute Board board) throws Exception {
        List<Board> list = this.boardService.selectBoardList(request, board);
        return ResponseEntity.ok().body(list);
    }

    //상세
    @GetMapping("{boardIdx}")
    public ResponseEntity<Board> boardDetail(HttpServletRequest request, @PathVariable(name="boardIdx", required=true) int boardIdx) throws Exception {
        Board detail = this.boardService.selectBoard(request, boardIdx);
        return ResponseEntity.ok().body(detail);
    }

    //등록
    @PostMapping
    public ResponseEntity boardInsert(@RequestBody Board board) throws Exception {
        Integer idx = this.boardService.insertBoardSelectKey(board);
        return ResponseEntity.created(linkTo(BoardApiController.class).slash(idx).toUri()).build();
    }

    //수정
    @PutMapping("{boardIdx}")
    public ResponseEntity<String> boardUpdate(@PathVariable(name="boardIdx", required=true) int boardIdx, @RequestBody Board board) throws Exception {
        int cnt = this.boardService.selectBoardCnt(boardIdx);
        if (cnt > 0) {
            board.setBoardIdx(boardIdx);
            this.boardService.updateBoard(board);
            return new ResponseEntity<>(HttpStatus.OK);
        } else {
            Integer idx = this.boardService.insertBoardSelectKey(board);
            return ResponseEntity.created(linkTo(BoardApiController.class).slash(idx).toUri()).build();
        }
    }

    //삭제
    @DeleteMapping("{boardIdx}")
    public ResponseEntity<String> boardDelete(@PathVariable(name="boardIdx", required=true) int boardIdx) throws Exception {
        int ret = this.boardService.deleteBoard(boardIdx);
        if (ret > 0) {
            return ResponseEntity.noContent().build();
        } else {
            return ResponseEntity.notFound().build();
        }
    }

}
```

<br><br>

이번 포스팅 끝.

<br>

---
[참고]
- https://en.wikipedia.org/wiki/HATEOAS
- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/ResponseEntity.html