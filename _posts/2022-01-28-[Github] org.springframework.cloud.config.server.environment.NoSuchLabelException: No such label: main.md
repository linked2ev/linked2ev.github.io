---
layout: post
title:  "[Github] org.springframework.cloud.config.server.environment.NoSuchLabelException: No such label: main"
subtitle:   "[Java]"
categories: spring
tags: spring-devhistory
comments: true
---

Springboot yml profile 설정시에 No such label 에러 포스팅

<br><br>


# 에러

> Error Log

```
org.springframework.cloud.config.server.environment.NoSuchLabelException: No such label: main
```

<br><br>


# 원인

최근 들어 IT내 여러 아키텍처, 개발 도구들이 master/slave 개념 등으로 인해, github에서 default branch를 master가 아닌 `main`으로 변경되었다.

그러나 여전히 master branch를 설정으로 인식하는 경우에 발생하는 에러이다. 

<br><br>


# 해결

repository의 default branch 설정값으로 변경

> .yml or .properties 파일

```yml
spring.cloud.config.server.git.default-label=main #or master..
```

<br><br>

---
[참고]
- https://stackoverflow.com/questions/40024161/spring-config-server-no-such-label-master