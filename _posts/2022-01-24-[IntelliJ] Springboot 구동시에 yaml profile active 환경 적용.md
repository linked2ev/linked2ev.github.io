---
layout: post
title:  "[IntelliJ] Springboot 구동시에 yaml profile active 환경 적용"
subtitle:   "[IntelliJ]"
categories: devsub
tags: intellij
comments: true
---

intellij 에서 yaml / yml 파일 프로파일 active 설정하는 포스팅이다.

<br><br>


# 에러

해당 에러의 주원인이 profile 설정문제는 아니나. 발생할 수는 있다.

```
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'beanName' defined in class path resource [/../../className.class]
```

<br><br>


# 해결

Run -> Edit Configuartions  

<br>

> VM options

```
-Dspring.profiles.active=dev
```

[![intellij-vm-profiles-active-s1](/assets/img/2022/intellij-vm-profiles-active-s1.png)]() <br>