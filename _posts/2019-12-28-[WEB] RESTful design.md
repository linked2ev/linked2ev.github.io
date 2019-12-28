---
layout: post
title:  "[WEB] RESTful 디자인"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

RESTful 디자인에 있어서 정리한 내용입니다.

<br>

요즘 웹어플리케이션에 있어서 react, angular, vue 등 javascript 진화, 넥사크로 등 UI 개발플랫폼 툴, 여러 타 기종, OPEN API, 독립적인 시스템 등 각 기술에 맞는 새로운 어플리케이션과 시스템이 만들어지고 리뉴얼하고 있다. 이런한 시대에 효율적인 디자인이 필요하다.

그래서 RESTful 디자인으로 독립적인 API서버를 설계하면 이러한 이슈들에 대해 시스템이 오래되어도 유연하고 확장성이 좋다.

<br>

RESTful 디자인에 있어서 `URI는 정보의 자원을 표현`해야 하며, `자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE)로 표현`한다.

해당 포스팅의 길어보이는 내용을 간단하게 정리한 표이다.

<br>

기능 | 메서드 | RESTful URI | 비교 URI
---- | ---- | ---- | ----
등록 | POST | /book | /book/insertBook
목록 | GET | /book | /book/getBookList
목록 | GET | /books | /book/getBookList
상세 | GET | /book/:id | /book/getBookDetail?id=113
수정 | PUT | /book/:id | /book/updateBook
삭제 | DELETE | /book/:id | /book/deleteBook


우선 기존 URI같은 경우는 URI내에 동사 명사 등이 중첩되고 get~, insert~, ~list, update~, delete~ 등 개발자들마다 동사표현이 정말로 상이하다. 그래서 select~, red~, mod~, del~ 등 URI가 다소 복잡해보인다.
<br>

RESTful URI 같은 경우는 리소스의 자원을 표현하면서 기능에 따라 메서드를 통해 설계하기 때문에 기존 URI보다 간결해져서 의미 파악이 직관적이고 가독성이 좋다.

<br><br>

# 1. URI 설계

- URI는 정보의 자원을 표현
    - 동사보단 명사를 사용
    - 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE 등)로 표현
- 슬래시 구분자(/)는 계층 관계를 나타내는데 사용
- URI 마지막 문자로 슬래시(/)를 포함하지 않는다.
- 하이픈( - )은 URI 가독성을 높이는데 사용
- 밑줄( _ )은 URI에 사용하지 않는다.
- URI 경로에는 소문자가 적합하다.
- 파일확장자는 URI에 포함하지 않는다.

<br>


## URI는 정보의 자원을 표현

URI는 동사보단 명사를 사용하여 정보의 자원을 표현해야 하고, 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE 등)로 표현

```
GET : /board/selectBoardList (x)
GET : /board/getBoard?id=1 (x)
POST : /board/getBoard (x)
```
```
GET : /board
GET : /board/1
POST : /board
```

<br>

> HTTP 메서드

REST API 리소스 모델에서 각 HTTP 메서드는 잘 정의된 고유한 의미가 있다. 일반적인 HTTP 메서드는 다음과 같다.

메서드 | 기능
---- | ----
GET | 해당 URI 리소스의 표현을 조회하며, 응답 메시지의 본문은 요청된 리소스의 세부 정보를 포함
POST | 해당 URI에 새 리소스를 생성하며, 요청 메시지의 본문은 새 리소스의 세부 정보를 제공
PUT | 해당 URI에 리소스를 생성하거나 수정하며, 요청 메시지의 본문은 생성/수정 할 리소스를 제공
DELETE | 해당 URI의 리소스를 삭제합니다.

<br><br>


## 슬래시 구분자(/)는 계층 관계를 나타내는데 사용

리소스 간의 계층 관계를 나타내는 데 사용한다.

```
http://restapi.com/board/faq
http://restapi.com/board/notice
http://restapi.com/goods/top
http://restapi.com/goods/pants
```

<br>

## URI 마지막 문자로 슬래시(/)를 포함하지 않는다.

URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 작용하며, 서로 다 른 URI 두 개는 서로 다른 리소스에 매핑된다. 

```
http://restapi.com/seller/score/ (x)
```
```
http://restapi.com/seller/score
```

<br>

만약, 클라이언트가 아래의 2개의 URI로 요청시에 오류로 처리하지 않고 둘다 "http://restapi.com/seller/score"로 요청된다.

```
http://restapi.com/seller/score/
http://restapi.com/seller/score
```

<br>

## 하이픈( - )은 URI 가독성을 높이는데 사용

URI를 쉽게 읽고 해석하기 위해, 긴 URI 경로에 하이픈을 사용해서 가독성을 높이기 위해 사용할 수 있다.

```
http://restapi.com/mypage/myShopPurchasePoint (x)
```

```
http://restapi.com/mypage/my-shop-purchase-point
```

<br>

## 밑줄( _ )은 URI에 사용하지 않는다.

텍스트 뷰어 애플리케이션은 클릭할 수 있다는 표시로 URI에 밑줄을 긋는다. 글꼴 에 따라 다르긴 하지만, 밑줄은 보기 어렵거나 밑줄 때문에 문자가 완전히 가려지 기도 한다. 

```
http://restapi.com/mypage/my_shop_purchase_point (x)
```

```
http://restapi.com/mypage/my-shop-purchase-point
```

<br>

## URI 경로에는 소문자가 적합하다.

RFC 3986은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정하고 있기에 대문자를 사용하면 문제가 될 수 있다.

```
http://restapi.com/mypage/myShopPurchasePoint (x)
```

<br>

## 파일확장자는 URI에 포함하지 않는다.

```
http://restapi.com/goods/113/display.jpg (X)
```

<br><br>


# 2. 리소스 형식인 도큐먼트(Document)와 컬렉션(Colllection)

REST API 디자인에서 보편적으로 쓰이는 리소스의 구조로 API는 리소스를 설계하는 리소스 형식들 중에 `도큐먼트(Document)`와 `컬렉션(Colllection)`이다. 이 외에 `스토어(Store)`와 `컨트롤러(Controller)` 형식도 있다.

<br>

## 도큐먼트

기본이 되는 리소스이며, 도큐먼트 리소스는 객체 인스턴스나 데이터베이스 레코드와 유사한 단일 개념이다. 도큐먼트 리소스는 자식 리소스를 가질 수 있으며 종속 개념을 표현한다.

아래는 URI는 각각 도큐먼트 리소스를 나타낸다.

```
http://restapi.com/category
http://restapi.com/category/health
http://restapi.com/category/health/medicine
http://restapi.com/category/health/medicine/113
```

<br>

## 컬렉션

도큐먼트의 디렉터리 리소스이며, 집합을 의미한다, 도큐먼트에 새로운 리소스를 생성해서 컬렉션에 포함시킬 수 있다.

아래는 각 URI는 컬렉션 리소스를 나타낸다.

```
http://restapi.com/leagues
http://restapi.com/leagues/premier/teams
http://restapi.com/leagues/premier/teams/chelsea/players
```


<br><br>



# 3. HTTP 메서드 기준 HTTP 응답 상태 코드

## GET 메서드

- 정상적으로 조회 할 경우 HTTP 상태 코드 `200(OK)`를 반환
- 리소스를 찾을 수 없는 경우 HTTP 상태 코드 `404(Not Found)`를 반환

<br>

## POST 메서드

- 새 리소스를 생성한 경우 HTTP 상태 코드 `201(Created)`을 반환
- 요청을 처리하지만 새 리소스를 만들지 않는 경우 메서드는 HTTP 상태 코드 `200(OK)`을 반환
- 반환할 결과가 없으면 메서드가 응답 본문 없이 HTTP 상태 코드 `204(No Content)`를 반환
- 클라이언트가 잘못된 데이터를 요청시 HTTP 상태 코드 `400(Bad Request)`을 반환

<br>

## PUT 메서드

- 기존 리소스를 수정한 경우 HTTP 상태 코드 `200(OK)` 또는 `204(No Content)`를 반환
- 새 리소스를 생성한 경우 HTTP 상태 코드 `201(Created)`을 반환
- 리소스가 불가능 또는 모순 상태이여서 수정을 못 할 경우 HTTP 상태 코드 `409(Conflict)`를 반환을 고려하여 설계

<br>

## DELETE 메서드

- 삭제처리가 성공해도 응답 본문에 추가 정보가 포함되지 않았음을 나타내는 HTTP 상태 코드 `204(No Content)`로 반환
- 삭제할 리소스가 없을 경우 HTTP 상태 코드 `404(Not Found)`로 반환

<br><br>


> 응답 상태 코드 범주

- 1xx: 정보 전송 프로토콜 수준의 정보 교환
- 2xx: 성공 클라이언트 요청이 성공적으로 수행됨
- 3xx: 클라이언트는 요청을 완료하기 위해 추가적인 행동을 취해야 함
- 4xx: 클라이언트 오류 클라이언트의 잘못된 요청
- 5xx: 서버 오류 서버쪽 오류로 인한 상태 코드

<br>

>  HTTP 응답 상태 코드

응답 상태 코드 | 설명
---- | ----
`200(OK)` | 클라이언트의 요청을 정상적으로 수행했을 경우
`201(Created)` | 해당 리소스가 성공적으로 생성된 경우
`204(No Content)` | 성공했으나 의도적으로 응답 본문에 추가 정보가 포함되지 않은 경우
`301(Moved Permanentl)`	| 클라이언트가 요청한 리소스에 대한 URI가 변경 되었을 때 사용하는 응답 코드, 응답 시 Location header에 변경된 URI 기술
`400(Bad Request)` | 클라이언트의 요청이 부적절 할 경우 사용하는 응답 코드
`401(Unauthorized)` | 클라이언트가 인증에 문제가 있을 때 응답 코드
`403(Forbidden)` | 클라이언트가 인증상태와 상관없이 허용된 범위 외의 리소스에 엑세스를 금지할 때 사용<br>(403 보다는 400이나 404를 사용할 것을 권고. 403 자체가 리소스가 존재한다는 의미이기에)
`404(Not Found)` | 요청 URI에 해당하는 리소스가 존재하지 않은 경우
`405(Method Not Allowed)` | 라이언트가 요청한 리소스에서 HTTP 메서드가 지원되지 않을 경우
`409(Conflict)` | 리소스 상태에 위반되는 행위를 했을 경우
`500(Internal Server Error)` | 서버에 문제가 있을 경우


---
[참고]

- [REST API 디자인 규칙 책](http://www.hanbit.co.kr/store/books/look.php?p_code=E9835405237)
- https://docs.microsoft.com/ko-kr/azure/architecture/best-practices/api-design#organize-the-api-around-resources
- https://meetup.toast.com/posts/92