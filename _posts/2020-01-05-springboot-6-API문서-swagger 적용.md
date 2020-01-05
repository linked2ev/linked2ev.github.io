---
layout: post
title: "6. springboot Swagger-UI 적용"
subtitle: "[springboot]"
categories: gitlog
tags: springboot-restful
comments: true
---

스프링부트에 API문서 자동화 도구인 스웨거를 적용하는 포스팅입니다.

<br><br>


> 개발 환경 및 내용

- OS: Window 10
- Dev Tool: Intellij - 2019.2.3
- JDK: 1.8(_221)
- JAVA: 8
- Framework: Springboot v2.1.8
- Build Tool: Gradle v3
- RDBMS: MariaDB
- #Lombok #Swagger-UI

---

> 관련 URL

- https://swagger.io
- [해당 샘플 프로젝트 github 주소](https://github.com/linked2ev/neo)

---


<br><br>



# Swagger-UI란?

- 일반적인 동작, 패턴 및 일관된 UI로 RESTful API를 공유한다.
- API가 발전함에 따라 문서를 최신 상태유지한다.
- API 문서를 생성, 시각화 및 유지 관리가 편리하다.
- 문서화된 API에 대해 테스트 또한 가능하다.

## `즉, API 문서를 자동화 해준다.`

<br><br>


# 왜 REST API 자동화 문서인가?

이제 많은 프로젝트들이 독립 된 서버, 분산 된 서버, 다른 서비스 Open API, MSA 프로젝트 등에서 개발자나 기획자들간에 요청할때마다 하나하나 API에 대해서 파라미터 결과값 등에 데이터들에 대해 설명하거나 문서를 작성해서 메일을 주고 받고 협업하기에는 한계가 있기에

API문서 자동화해서 사용자들에게 주는 것이다. 해당 포스팅에서 다루는 Swagger-ui와 스프링에서 제공하는 Spring Rest Docs 등이 있다.

<br><br>


# 그러면 어떻게 문서화하고 자동화할까?

개발하기 전부터 RESTful API를 설계 해놓고 개발자들이 개발시에 각 API와 model 또는 domain에 어노테이션을 이용해 API를 문서화 할 수 있다. 그리고 그것을 Swagger가 API 최신상태를 문서로 동기화해준다.

swagger 라이브 데모를 http://petstore.swagger.io 를 통해 우선 확인해보자. 

<br><br>


# Springboot Swagger 적용

아래와 같이 간단하게 적용해보자.

<br>

> build.gradle dependency 추가

```gradle
implementation 'io.springfox:springfox-swagger2:2.9.2'
implementation 'io.springfox:springfox-swagger-ui:2.9.2'
```

> SwaggerConfiguartion.java

```java
@Configuration
@EnableSwagger2
public class SwaggerConfiguartion {

    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
            .select()
            .apis(RequestHandlerSelectors.any()) // 모든 RequestMapping URI 추출
            // .apis(RequestHandlerSelectors.basePackage("com")) // 패키지 기준 추출
            .paths(PathSelectors.ant("/v2/**")) // 경로 패턴 URI만 추출
            .build()
            .apiInfo(apiInfo());
    }

    private ApiInfo apiInfo() {
        return new ApiInfo(
            "NEO REST API ", //title
            "스프링부트 샘플 프로젝트", //description
            "v2", //version
            "서비스 약관 URL", //termsOfServiceUrl
            "linked2ev", //contactName
            "License", //license
            "localhost:8080"); //licenseUrl
    }
}
```

<br>

이제 http://localhost:8080/swagger-ui.html 에 접속하면 자신의 API문서를 확인할 수 있다.

<br>

[![swagger-api-s1.png](/assets/img/2020/01/swagger-api-s1.png)]()

<br><br>


# Swagger에 API문서(설명) 작성하기

현재 작성 된 API는 우리가 board는 게시판이라는 인식이 있기에 가독성이 좋아보이지만 실제로는 설명이 부족하다. 그래서 스웨거에 설명을 작성하기 위해서 자바에서 어노테이션을 통해서 API문서를 작성해보겠다.

아래의 스웨거의 어노테이션하고 옵션들을 직접 구현해보길 추천드립니다.


<br>

> BoardApiController.java

```java

// RESTful v2
@Api(tags = "BoardApiController", description = "게시글 API")
@Slf4j
@RestController
@RequestMapping("/v2/api/board")
public class BoardApiController {

    @Autowired
    private BoardService boardService;

    @ApiOperation(value="게시글 목록")
    @ApiImplicitParams({
        @ApiImplicitParam(name = "boardIdx", value="게시글번호", dataType = "int", paramType="query"),
        @ApiImplicitParam(name = "title", value="제목", dataType = "String", paramType="query"),
        @ApiImplicitParam(name = "contents", value="내용", dataType = "String", paramType="query")
    })
    @GetMapping
    public ResponseEntity<List<Board>> boardList(HttpServletRequest request, @ApiIgnore @ModelAttribute Board board) throws Exception {
        List<Board> list = this.boardService.selectBoardList(request, board);
        return ResponseEntity.ok().body(list);
    }

    @ApiOperation(value="게시글 상세")
    @ApiImplicitParams({
        @ApiImplicitParam(name = "boardIdx", value="게시글번호", required=true, dataType = "int", paramType="path")
    })
    @GetMapping("{boardIdx}")
    public ResponseEntity<Board> boardDetail(HttpServletRequest request, @PathVariable(name="boardIdx", required=true) int boardIdx) throws Exception {
        Board detail = this.boardService.selectBoard(request, boardIdx);
        return ResponseEntity.ok().body(detail);
    }

    @ApiOperation(value="게시글 등록")
    @PostMapping
    public ResponseEntity boardInsert(@RequestBody Board board) throws Exception {
        Integer idx = this.boardService.insertBoardSelectKey(board);
        return ResponseEntity.created(linkTo(BoardApiController.class).slash(idx).toUri()).build();
    }

    @ApiOperation(value="게시글 수정")
    @ApiImplicitParams({
        @ApiImplicitParam(name = "boardIdx", value="게시글번호", required=true, dataType = "int", paramType="path")
    })
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

    @ApiOperation(value="게시글 삭제")
    @ApiImplicitParams({
        @ApiImplicitParam(name = "boardIdx", value="게시글번호", required=true, dataType = "int", paramType="path")
    })
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

> Board.java

```java
@ApiModel(description = "게시글 모델")
@Getter @Setter
@ToString
public class Board implements Serializable  {

    @ApiModelProperty(value = "게시글 번호", dataType = "Integer", required = true, example = "1")
    public Integer boardIdx;

    @ApiModelProperty(value = "제목", required = true, example = "샘플 제목")
    public String title;

    @ApiModelProperty(value = "내용", required = true, example = "샘플 내용")
    public String contents;

    @ApiModelProperty(value = "노출여부", required = true, example = "Y")
    public String displayYn;

    @ApiModelProperty(value = "조회수", example = "11")
    public int hitCnt;

    @ApiModelProperty(value = "등록 아이디", example = "TEST01")
    public String regId;

    @ApiModelProperty(value = "등록 날짜", example = "20200101")
    public String regDate;

    @ApiModelProperty(value = "수정 아이디", example = "TEST01")
    public String modId;

    @ApiModelProperty(value = "수정 날짜", example = "20200101")
    public String modDate;
}
```

<br><br>

작업을 완성하면 아래와 같이 설명이 노출 된다.


[![swagger-api-s2.png](/assets/img/2020/01/swagger-api-s2.png)]()

[![swagger-api-s3.png](/assets/img/2020/01/swagger-api-s3.png)]()

[![swagger-api-s4.png](/assets/img/2020/01/swagger-api-s4.png)]()

<br><br>


# 그 외 소소한 Tip

> @ApiIgnore

등록, 수정 등에 메서드 parameter에 @RequestBody나 @ModelAttribute로 model 객체로 받지만, API 문서에 parameters에 model 객체가 아닌 요청 파라미터로 보여주고 싶다면 @ApiImplicitParam로 실제 요청 파라미터를 작성하고 파라미터 앞에 `@ApiIgnore`를 명시하면 된다.

<br>

```java
@ApiOperation(value="게시글 수정")
@ApiImplicitParams({
    @ApiImplicitParam(name = "title", value="제목", dataType = "String", paramType="query"),
    @ApiImplicitParam(name = "contents", value="내용", dataType = "String", paramType="formData"),
    @ApiImplicitParam(name = "displayYn", value="노출여부", dataType = "String", paramType="formData")
})
@PutMapping("{boardIdx}")
public ResponseEntity<String> boardUpdate(@PathVariable(name="boardIdx", required=true) int boardIdx, @ApiIgnore @RequestBody Board board) throws Exception {
```

<br><br>

>스웨거의 장점은 API문서 자동화지만, 단점은 그 많은 어노테이션의 작성과 그에 따른 코드가 사뭇 복잡해보이고, 옵션들을 잘 구색을 맞추지 않으면 API문서 또한 지저분해 보이고 혼돈이 올 수 있다. 
<br><br>
현실은 개발일정과 요청변경 때문에 어노테이션 작성도 힘들지만,최소한이라도 잘 사용하면 효율적일 듯 하다. 

<br><br>

ㅡ 끝 ㅡ