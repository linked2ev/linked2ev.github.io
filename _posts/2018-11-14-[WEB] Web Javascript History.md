---
layout: post
title:  "[WEB] Javascript Web Service 발전"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

이 포스팅의 주제가 Javascript Web Service 발전인데 포스팅 내용과 맞는지 잘 모르겠다.  

우선 작성자가 아직 코린이고 jQuery부터 학습한 비전공자이기에 많은 javascript 여러 라이브러리들을 직접적으로 접한 프로젝트는 많지 않지만 중국어를 공부 할 때도 이상하게 문법을 좋아했는데 그래서 그런지 이런 것들(?)에 재미를 느낀다.

<br>


## 1. Javascript? ECMAScript? ES5? ES6?
---

Javascript가 발전하면서 ES5, ES6 또 typescript 등 키워드를 많이 접하는데 자바스크립트는 왜 `ECMAScript`라 불리게 되었나?

<br>

> 자바스크립트가 나온 이후, MS사는 IE 3.0에서 동작하는 "JSrcipt"라는 똑같은 언어를 만들어 냈다.  
<br>
그러자 넷스케이프사는 자바스크립트를 표준화 하기 위해, 표준화 기구인 Ecma International에 요청을 했다.  
<br>
1996년 11월, ECMA-262라 불리는 명세서에 대한 작업이 시작했고 그 버전은 1997년 7월에 완성되었다.  
<br>
당시 Sun사가 "JAVA"라는 단어를 상표 등록을 해 놨기에 "JavaScript"라고 부를 수 없었다.
<br>
따라서, 다음과 같이 이름이 결정되었다: 표준 언어의 이름은 ECMAScript이고,
<br>
대외적으로 알리기 위해서 자바스크립트나 J스크립트라고 불렀다. 나중엔 표준 이나 구현 모두 "JavaScript"라는 이름으로 불렸다.
<br>  
우리 흔히 쓰는 버전이 ES5, 최근에 let, const, Arrow 함수 등이 ES6이다.

<br>


## 2. Ajax 이전 웹 애플리케이션
---

이전의 웹 애플리케이션은 폼을 submit해서 웹 서버로 요청을 하면 웹 서버는 데이터를 처리하고 새로운 웹 페이지를 작성하고 응답하는 JSP 개념이다.

<br>

## 3. Ajax 등장, View 생성
---

이때 당시 Google의 추천 검색어가 핫했다고 한다. 그러면서 prototype.js와 jQuery가 나왔고 웹 화면 개발이 더욱더 동적으로 변화되고 기술이 생겼다.

또한 심플해 보이는 jQuery 통해 Dom을 쉽게 제어할 수 있었다.

> javascript vs jQuery  

```js
// javascirpt
document.getElementById("divId");
// jQuery
$("#divId");
```

<br>

> 초기 javascript, View 생성

```js
// 배열 객체에 담아 사용하는 방식
var arrHtml = new Array();
arrHtml['strHtml'] = new Array();
arrHtml['strHtml'][0] = { html: '<input type="text" name="test" />' };
arrHtml['strHtml'][1] = { html: '#TEST#'};
arrHtml['strHtml'][2] = { html: '<button type="button" onClick="delCate(this)">삭제</button>' };

// 문자열에 담아 사용하는 방식
var strHtml = '';
strHtml += '<tr>';
strHtml += '<td>';
strHtml += '<input type="text" name="cate" value="'+test+'" />';
strHtml += '<input type="text" name="cateDefault" value="'+test+'" />';
strHtml += '</td>';
strHtml += '<td><button type="button" onClick="delCate(this)">삭제</button></td>';
strHtml += '</tr>';

// ...(생략)...
$('#target_id').find('div').append(strHtml);
```

<br>

위와 같이 개발을 하고 모듈화 하며 많이 본 코드들이다. 사실 불편함이 없다고 생각되고 잘 돌아가서 만족하면 문제는 아니다. 하지만 HTML과 javascript가 섞이면 개발자와 퍼블리셔와 서로간의 능력에 따라 일의 경계가 모호해지고 형상관리 또한 복잡해진다.  

또한 위에 예제 소스는 간결하고 부분이지만 막상 프로젝트에서는 아무리 잘 짜려고 해도 요구사항이 변하고 로직이 추가되면 가독성이 떨어진다.

<br>


## 4. Template Engine, 템플릿
---

Template, mustach... 등 콧수염이라고 불린다. 하여튼 Template에 Data를 바인딩하여 HTML를 생성하는 개념이다.

<br>

> Template View 렌더링

```js
<tr>
	<td><input type="hidden" name="no" value="{ {no} }" /></td>
	<td><input type="text" name="gbn" value="{ {gbn} }" /></td>
	<td><input type="text" name="item{ {no} }" maxlength="50" value="{ {itemName} }" /></td>
</tr>
```

dom.js, mustach.js, underscore.js, handlebars.js jade.js 등 다양한 `템플릿 엔진` 라이브러리들이 등장하고 Spring boot에서도 템플릿 등을 공식으로 지원하기 시작했다. 그리고 
View를 처리하기 위해 여러방식으로 환경에 맞게 템플릿 엔진을 혼합해서 사용되고 있다.

<br>


## 5. Front-End Framework, 프론트엔드 프레임워크
---

서버 언어 Java에 Spring framework MVC 패턴처럼, 프론트엔드에서도 프레임워크 개념이 생깁니다.  

그 중에서 `Backbone.js`은 라이브러리 형태로 구조를 지원했고 그 이후에 프론트엔드 프레임워크 개념이 잡힌 `Ember.js`, `Knockout.js` 등이 등장합니다.

<br>

그리고 드디어 구글에서 `Angular.js`가 나타납니다.

실제로 AngularJS는 html파일과 js파일 따로 분리하면서 MVC패턴이 명확하고 Controller, Directive, Filter, Service 모듈화하기에 100% AngularJS 활용을 안하더라도 프로젝트 전체 구조를 쉽게 알 수 있고 개발 및 가독성이 좋다.

하지만 AngularJS는 Angular2부터는 호환 이슈가 있고, 렌더링 속도나 외부서비스 콜백서비스 이슈(ex. 결제 페이지)가 있다. 그리고 SEO 이슈이다.

<br>

그리고 이 뒤에 페이스북에서 DOM대신 Virtual DOM이라는 걸 이용한 View에 집중한 라이브러리 `React.js`를 발표했습니다. 또한 Angular의 단점인 SEO문제를 보완하고 서버 사이드 렌더링을 고려하여 설계되었습니다. 웹앱의 단점도 보완되어 이후에는 `React-Native`도 발표 되고 React로 개발한 프레임워크 `Next.js` 등 도 나왔다.

<br>

이후에는 `Vue.js`와 `Nuxt.js` 등이 나오고 꾸준히 Angular 버전이 높아지고 있다. 뭔가가 끝없이 계속 나온다.

<br>

---
참고  
+ [http://wit.nts-corp.com/2014/08/13/1925](http://wit.nts-corp.com/2014/08/13/1925)