---
layout: post
title:  "[Springboot] 4. MVC 패턴 구조로 변경 STEP1"
subtitle:   "[Springboot]"
categories: gitlog
tags: springboot
comments: true
---

Springboot 프로젝트 초기 개발 환경설정은 저번 포스트에서 끝이 났다.  
이번 포스트부터는 스프링MVC 패턴 형태로 진행하겠다.  
(임시로 스프링부트에서 jsp로 진행 후 걷어 낼 예정)


## 7. 패키지 구조 변경

7-1. 패키지 구조 변경  

우선 패키지 구조부터 아래와 같이 변경하고, NoticeBoradController.java 추가

[![MVC Struct STEP1](/assets/img/bmw/201807/2018-07-25-mvcStructureStep1.png)]() 
<br>

> NoticeBoradController.java

```java
package com.bmw.board.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class NoticeBoardController {

	@RequestMapping("/board/noticeBoard")
	public String home() {
		return "Hello, noticeBoard!";
	}
	
}
```
- board는 공지게시판, FAQ, 1:1문의 등 게시판를 관리하기 위한 패키지명
- 각 패키지 밑에 controller와 service 개념으로 패키지를 구분
- 스프링부트 설정 클래스는 com 패키지 바로 밑으로 이동

DemoApplication.java 기준으로 하위 패키지들을 스캔하여 Bean을 찾고 등록하기 때문에 상위 패키지로 이동한다.
<br><br>

7-2. 메인 어플리케이션 구동 설정 변경  
DemoApplication.java 경로가 바뀌었기 때문에 아래와 같이 설정한다.

프로젝트 우클릭 > Properties > Run/Debug Setting 클릭

[![MVC Struct STEP2](/assets/img/bmw/201807/2018-07-25-mvcStructureStep2.png)]() 
<br>
 
Main tpye을 DemoApplication.java 경로에 맞게 수정해준다.
<br><br>


7-3. 다시 localhost:8070/home 과 localhost:8070/board/noticeBoard 접속  

둘다 정상적으로 출력이 되면 정상이다.

[![MVC Struct STEP3](/assets/img/bmw/201807/2018-07-25-mvcStructureStep3.png)]() 
<br>

다음 포스팅에서는 스프링부트에서 jsp로 사용하는 것을 진행하겠습니다.
<br><br>

---
스프링부트에 대해 이미 고견이 있는 고수님들의 블로그들이 많기에
해당 포스트에서는 개인프로젝트 개발 진행과정에 초점을 두겠습니다.