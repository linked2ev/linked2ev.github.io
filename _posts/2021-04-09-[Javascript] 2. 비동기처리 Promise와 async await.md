---
layout: post
title:  "[Javascript] 2. 비동기처리 Promise와 async/await"
subtitle: "[Javascript]"
categories: javascript
tags: javascript-devhistory
comments: true
---


비동기처리 Promise와 async/await

<br><br> 


# 콜백함수와 Promise

ES6 기반에서 개발하다 이전 프로젝트에서 ES5 환경에서 여러 오픈 js 라이브러리들을 적용해서 개발하다 보면
`콜백함수가 너무 많아져서 콜백지옥이 발생하거나 비동기처리(자바스크립트 로직 실행 순서)`를 제어하기가 어려워진 경험을 겪다보니
간단하게 Promise와 async/await 에 대한 정리이다.

<br>

- 동기 : 요청을 보낸 후 해당 응답을 받아야 다음 동작을 실행
- 비동기 : 요청을 보낸 후 응답에 관계 없이 다음 동작을 실행
- 비동기처리 : 특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고, 순차적으로 다음 코드를 먼저 실행하는 자바스크립트의 특성(`싱글 스레드, 콜스택`)

<br><br>


## 비동기처리
---

<br>

아래와 같이 서버 요청 후에 서버에서 처리하는 동안 기디라지 않고 다음 코드를 자바스크립트는 실행하기 때문에
원치 않은 답이 상황이 온다.

<br>

```js
function getData() {
	let retData;
	$.get('url주소/products/1', function(response) {
		retData = response;
	});
	return retData;
}

//서버에 api요청 후에 기다리지 않고 코드 실행
console.log(getData()); // undefined
```

<br><br>


## Promise
---

<br>

Promise는 비동기 연산이 종료된 이후의 결과값이나 실패 이유를 처리하기 위한 처리기를 연결할 수 있도록 합니다. 마치 동기 메서드처럼 값을 반환할 수 있다

<br>

> 프로미스의 3가지 상태(states) = 처리 과정

- 대기(pending): 이행하거나 거부되지 않은 초기 상태.
- 이행(fulfilled): 연산이 성공적으로 완료됨.
- 거부(rejected): 연산이 실패함.

<br>


`아래 콜백함수와 Promise 2개의 코드는 동일한 처리이다.`

<br>

> 콜백함수

```js
// 콜백 함수
function getData(callbackFunc) {
	$.get('url주소/products/1', function (response) {
		if (! response) {
			console.log('Request is failed');
		}
    	callbackFunc(response); // 서버에서 ajax 처리 후 callbackFunc() 호출
	});
}

getData(function (data) {
	console.log(data);
});
```

<br>

> Promise

```js
function getData() {	
	return new Promise(function (resolve, reject) {
    	$.get('url주소/products/1', function (response) {
			if (! response) {
				reject(new Error('Request is failed'));
			}
			resolve(response);
    	});
  	});
}

// Fulfilled 또는 Rejected의 결과 값 출력
getData().then(function (data) {
	console.log(data);
}).catch(function (err) {
	console.error(err);
});
```

<br>

webpack 등을 이용해서 ES6 개발환경이면 상관없지만, ES5 개발 환경에서 IE에서는 Promise 를 지원안해준다. IE Promise polyfill 로 검색해서 바벨에서 지원하는 라이브러리를 import해서 개발해야한다. 역시 좋은 곳에서 개발하는게 좋다.

<br><br>



# async/await 

async/await 접해보면 javascript가 왜 뜨는지 알 수가 있다. 비동기처리를 위한 콜백함수 패턴의 단점을 커버해서 개발하기도 편하고 코드 품질도 좋아지는게 느껴진다. 위에 콜백함수나 프로미스로 이용한 코드보다 더 간결하고 가독성이 좋다.

<br>

> async/await 

위에 있는 코드와 동일하다. async/await로 동기처리 해주며, async/await 를 없애면 !!가 있는 주석 라인의 response는 undefined가 출력 된다.

```js
async function getData(callbackFunc) {
	let response = await $.get('url주소/products/1');
	if (! response) {
		console.log('error');
	}
	console.log(response);//!! 
	callbackFunc(response);
}
```

<br>

> 다른 예제 코드

```js
function getData() {
	let response = await $.get('url주소/products/1');
	//...코드
	return response;
}

async function fnEmployee () {
	let ret = await getData();
	if (! ret) return;
	//...코드
}
```
 