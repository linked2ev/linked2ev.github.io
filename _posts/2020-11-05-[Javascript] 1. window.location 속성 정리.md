---
layout: post
title:  "[Javascript] 1. window.location 속성 정리"
subtitle: "[Javascript]"
categories: javascript
tags: javascript-devhistory
comments: true
---

안쓰면 기억이 안남


# window.location

- window.location.['속성']
- exampleURL= 'https://developer.mozilla.org:8080/en-US/search?q=URL#search-results-close-container'

<br>

속성 | 결과
--- | ---
href | https://developer.mozilla.org:8080/en-US/search?q=URL#search-results-close-container
protocol | https:
host | developer.mozilla.org:8080
hostname | developer.mozilla.org
port | 8080
pathname | /en-US/search
search | ?q=URL
hash | #search-results-close-container
origin | https://developer.mozilla.org:8080


<br><br>



---
[참고]
- https://developer.mozilla.org/ko/docs/Web/API/Location
[관련]
- https://developer.mozilla.org/ko/docs/Web/API/URLSearchParams