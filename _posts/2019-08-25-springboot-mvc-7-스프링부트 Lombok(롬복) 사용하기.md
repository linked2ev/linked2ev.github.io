---
layout: post
title:  "7. 스프링부트 Lombok(롬복) 사용하기"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

스프링부트 Lombok(롬복) 사용해보자. 개인적으로 Lombok은 단순하게 편리를 위해 쓰이거나 공부를 위한 것을 떠나, 기본적인거는 다룰 줄 알아야 한다고 생각된다.

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
- #Lombok

---

<br><br>


# 1. Lombok(롬복)이란?

프로젝트가 객체 중심으로 개발하던 데이터 중심으로 개발하던 개발자들은 VO/DTO/Domain 객체를 생성해 getter/setter 그리고 toString 메서드를 만듭니다. 처음에 개발 초기에는 깔끔하게 하지만 잦은 수정과 필드명 변경 추가에 따라 관리가 쉽지 않습니다.

그래서 롬복(Lombok)은 자바 클래스를 만들때 자주 사용되는 getter/setter나 toString 등의 코드를 애너테이션으로 대체해서 선언하고 java 코드를 컴파일 할 때 그에 맞는 코드를 생성해주는 것이다. 

<br><br>


# 2. Lombok 의존성 추가 및 설치


## Lombok 의존성 추가

Lombok 외부라이브러리를 사용하기 위해 의존성을 추가하고, 컴파일 시 새로운 플러그인 에러이슈 떄문에 아래와 같이 의존성 및 설정을 추가한다.

프로젝트 생성 시 Lombok를 생성했으면 추가 할 필요 없다.

> build.gradle

```gradle
configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

dependencies {
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

<br>

## Lombok 다운로드 

> Lombok 다운로드 - https://projectlombok.org/download

[![sts-mvc-7-01](/assets/img/devlog/201908/sts-mvc-7-01.png)]()

<br>

## Lombok 실행

다운받은 `lombok.jar 파일`을 실행하기 위해서
- 1 . cmd 창 접속 
- 2 . 해당 프로젝트 JDK 설치 경로 이동
    ```
    $ C:\project\neo\Java\jdk1.8.0_221\bin
    ```
- 3 . lombok.jar 파일 실행
    ```
    $ java -jar c:\project\neo\lombok.jar
    ```

<br>

## 해당 프로젝트 Eclipse 선택

[![sts-mvc-7-02](/assets/img/devlog/201908/sts-mvc-7-02.png)]()
[![sts-mvc-7-03](/assets/img/devlog/201908/sts-mvc-7-03.png)]()

<br>

## Eclipse에 설치

[![sts-mvc-7-04](/assets/img/devlog/201908/sts-mvc-7-04.png)]()
[![sts-mvc-7-05](/assets/img/devlog/201908/sts-mvc-7-05.png)]()

이렇게 설치는 끝이다.

<br><br>


# 3. Lombok 적용

## Ecplise 내 Generate 기능
---

Eclipse 내에 있는 기능을 사용할 경우 필드명을 수정하거나 추가 삭제 할 경우 작업이 많이 그에 따른 toString 메서드 또한 작업이 필요하다.

> boardDto.java
 
```java
package neo.apps.board.dto;

    private static final long serialVersionUID = 1L;

    public int boardIdx;

    public String title;

    public String content;

    public int hitCnt;

    public String regId;

    public String regDate;

    public int getBoardIdx() {
        return boardIdx;
    }

    public void setBoardIdx(int boardIdx) {
        this.boardIdx = boardIdx;
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

    public int getHitCnt() {
        return hitCnt;
    }

    public void setHitCnt(int hitCnt) {
        this.hitCnt = hitCnt;
    }

    public String getRegId() {
        return regId;
    }

    public void setRegId(String regId) {
        this.regId = regId;
    }

    public String getRegDate() {
        return regDate;
    }

    public void setRegDate(String regDate) {
        this.regDate = regDate;
    }

    @Override
    public String toString() {
        return "BoardDto [boardIdx=" + boardIdx + ", title=" + title + ", content=" + content + ", hitCnt=" + hitCnt
                + ", regId=" + regId + ", regDate=" + regDate + "]";
    }
}
```

<br>


## Lombok 애너테이션 사용
---

boardDto.java를 아래와 같이 코드가 전부이다. 애너테이션을 선언함으로써 개발자가 구현 할 개발 코드는 줄어들게 되고 그에 따른 추가 작업이 줄어든다.

간단한 기능만 설명하면 @Getter @Setter 애너테이션은 보시다시피 getter/setter를 대신하고 @ToString은 toString() 메서드를 대신한다.

> boardDto.java

```java
@Getter @Setter
@ToString
package neo.apps.board.dto;

    public Integer boardIdx;

    public String title;

    public String contents;

    public int hitCnt;

    public String regId;

    public String regDate;
}
```

> 필드 출력

```java
// 상세정보
BoardDto boardDetail = this.boardService.selectBoardDetail(request, boardIdx);
System.out.print(boardDetail);
```

> Generate toString()

```
BoardDto [boardIdx=2, title=테스트 제목 2, content=테스트 게시판 내용입니다. 2, hitCnt=3, regId=admin, regDate=2019-08-24]
```


> Lombok 사용시

```
BoardDto(boardIdx=2, title=테스트 제목 2, content=테스트 게시판 내용입니다. 2, hitCnt=3, regId=admin, regDate=2019-08-24)
```

<br><br>


# 4. Lombok 애너테이션 추가 설명

> Lombok 애너테이션 설명

- https://goddaehee.tistory.com/95
- https://kkiuk.tistory.com/91

> Lombok 사용 설명

- https://kkiuk.tistory.com/91


<br><br>


---
[참고]
- https://projectlombok.org/