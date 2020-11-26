---
layout: post
title:  "1. [Thymeleaf] Springboot + Thymeleaf 프로젝트"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-thymeleaf
comments: true
---

`Springboot + Thymeleaf` 프로젝트에 대한 포스팅이다. 타임리프는 표현식이나 내장함수는 쉬우며 문서 자체가 잘 되어 있어서 초기 적용 방법만 알면 큰 문제는 없을 듯 하다.

<br><br>


> 개발 환경 및 내용

- Dev Tool: IDEA - IntelliJ
- JDK: OpenJDK
- JAVA: 11
- Framework: Springboot v2.4.0
- Build Tool: Gradle v3
- Springboot + Thymeleaf

<br><br>


# Thymeleaf 란

`Thymeleaf`는 순수 HTML에 HTML5문법을 사용하여 Server side 로직을 수행할 수 있는 HTML 태그 및 속성 기반의 `Template Engine`이다.
웹 및 독립 실행 형 환경을위한 최신 서버 측 Java 템플릿 엔진이며, 스프링부트에서도 jsp보단 템플릿 종류인 Thymeleaf, Mustache, Velocity, Freemarker 등을 공식적으로 권장하고 있다.

타임리프 또한 템플릿 방식이고 표현식 등이 다른 라이브러리와 비슷하기에 대한 기본적인 적용방법과 전체적인 구조만 포스팅 할 예정이다.

<br>


# Thymeleaf 개발 환경

> build.gradle

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-web-services'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

<br>

> Thymeleaf dependency 

```gradle
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect' /* 레이아웃 */
```

<br><br>



# Thymeleaf 표현식

> th:[속성]="${출력 값 또는 조건식}"  

```html
<span th:text="${boardId}"></span>
<input name="boardId" th:value="${boardModel.boardId}" type="text">
```

<br>

> Thymeleaf 3.x 에서는 inline 표현식이 추가  

```html
[[$board.title]]
```

타임리프의 다른 표현식이나 내장함수나 뒤에서 포스팅

<br><br>



# Thymeleaf 프로젝트 구조

<br>

[![thymeleaf_s01](/assets/img/2020/11/thymeleaf_s01.png)]()

<br><br>

## commonHead.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<th:block th:fragment="commonHead">
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <meta content='width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no' name='viewport'>
    <meta charset="utf-8">
    <meta name="title" th:content="${metaTitle}">
    <title th:text="${metaTitle}"></title>
    <meta http-equiv="X-UA-Compatible" content="IE=Edge">

    <!-- js -->
    <!-- <script src="@{https://code.jquery.com/jquery-3.5.0.min.js}"></script>-->
    <script th:src="@{/assets/js/jquery/jquery-3.5.0.min.js}"></script>
    <script th:src="@{/assets/js/jquery/jquery-ui.min.js}" ></script>

    <script th:src="@{/assets/js/libs/vue.2.6.12.js}"></script>
    <script th:src="@{/assets/js/libs/axios.min.js}"></script>

    <!-- css -->
    <link rel="stylesheet" th:href="@{/assets/css/common/custom-ui.css}" />

</th:block>
</html>
```

<br><br>


## header.html  

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<header th:fragment="header">
    <div>
        <h3>Layout Common Header</h3>
        <h3>메뉴1 | 메뉴2 | 메뉴3</h3>
        <hr>
    </div>
</header>
</html>
```

<br><br>


## footer.html  

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<footer th:fragment="footer">
    <div>
        <hr>
        <h3>Layout Common Footer</h3>
    </div>
</footer>
</html>
```

<br><br>


## defaultLayout.html `(기본 레이아웃)`
 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      lang="ko">
<head>
    <th:block th:replace="fragments/commonHead"></th:block>
</head>

<header th:replace="~{fragments/header :: header}" />

<body>
    <div>
        <!-- 각 화면 컨텐츠 -->
        <th:block layout:fragment="content" />
    </div>

    <!-- 각 화면 js -->
    <th:block layout:fragment="custom_js"></th:block>
</body>

<footer th:replace="fragments/footer :: footer" />
</html>

```

<br><br>


## view.html `(각 컨테츠 개발 화면)` 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layout/defaultLayout}"
      th:with="metaTitle='게시판'">
<head>
</head>

<body>
<!-- content -->
<th:block layout:fragment="content">
    <div>
        <h2><span th:text="${boardId}"></span>번 - 게시판 </h2>
    </div>
</th:block>
<!-- //content -->

<!-- script -->
<th:block layout:fragment="custom_js">
    <script th:src="@{/assets/views/board/view.js}"></script>
</th:block>
<!-- //script -->

</body>
</html>
```

<br><br>


# Mapping & URL 결과 호출

```java
@RestController
@RequestMapping("/views/board/")
public class BoardController {

    @GetMapping("/{boardId}")
    public ModelAndView view(@PathVariable(name="boardId", required = true) Long boardId) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("boardId", boardId);
        mv.setViewName("views/board/view");
        return mv;
    }
}
```

<br>


[![thymeleaf_s02](/assets/img/2020/11/thymeleaf_s02.png)]()

<br><br>

스프링부트에서 thymeleaf(타임리프)로 화면 개발 할 경우 위와 같이 html/css/js 파일들을 include/import 해서 프로젝트 구조만 파악하기 위한 포스팅이다.

https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#introducing-thymeleaf


<br><br>

---  
[참고]
- https://www.thymeleaf.org/index.html
- https://www.thymeleaf.org/doc/articles/layouts.html
- https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#introducing-thymeleaf
- https://eblo.tistory.com/57
- https://elfinlas.github.io/2018/02/16/thymeleaf-layout-dialect_exam/
- https://elfinlas.github.io/2018/02/17/thymeleaf-layout-dialect/

---

- https://code.jquery.com/
- https://cdnjs.com/libraries/axios
- https://unpkg.com/vue@2.6.12/dist/vue.js

