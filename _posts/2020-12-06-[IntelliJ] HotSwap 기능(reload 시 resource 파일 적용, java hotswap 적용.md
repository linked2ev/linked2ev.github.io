---
layout: post
title:  "[IntelliJ] HotSwap 기능(reload 시 resource 파일 적용, java 코드 수정 시)"
subtitle:   "[IntelliJ]"
categories: devsub
tags: intellij
comments: true
---

IntelliJ에서 서버 재가동(컴파일) 없이 reload 할 경우 리소스 파일 수정 된 내용 바로 적용 및 java 코드 수정 시에도 컴파일 없이 hotswap 기능 적용

<br><br>




# 1. Intellij 에서 HotSwap 기능 (reload 시 resource 파일 적용)

## 1-1. 의존성 추가 (gradle, maven)

> build.gradle

```gradle
dependencies {
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
}
```

<br><br>


# 1-2. 빌드 프로젝트 자동 체크박스 선택

1 ) Intellij IDEA > Preferences > Build, Execution, Deployment > Compiler 클릭  
2 ) `Build project automatically 체크`

<br>

[![intellij-hotswap-s1](/assets/img/2020/12/intellij-hotswap-s1.png)]()

<br><br>



# 1-3. 스프링부트 설정 파일에서 devtools 옵션

> application.yaml

```yaml
spring:
  devtools:
    restart:
      enabled: false
```

<br><br>


# 2. Java 코드 수정시 Hotswap 적용

1 ) Run > Edit Configurations

<br>

[![intellij-hotswap-s2](/assets/img/2020/12/intellij-hotswap-s2.png)]()

<br><br>


---

[참고]
- https://choigaram.tistory.com/2

