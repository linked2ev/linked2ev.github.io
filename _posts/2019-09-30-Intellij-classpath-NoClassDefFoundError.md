---
layout: post
title:  "[IntelliJ] Classpath 설정 - java.lang.NoClassDefFoundError:"
subtitle:   "[IntelliJ]"
categories: devsub
tags: intellij
comments: true
---

`java.lang.NoClassDefFoundError:` 비슷한 이슈 발생 시 또는 resource 파일을 사용하기 위해 Classpath 위치 설정 관련 포스팅이다. 큰 도움 될란지..

<br><br>


# ISSUE LOG

```
java.lang.NoClassDefFoundError:
```

<br>


# ISSUE 해결

JVM이 프로그램을 실행할 때, 클래스파일을 찾는 데 기준이 되는 파일 경로를 말하는 것인데 아래와 같이 설정 할 수 있다.

<br>

## File > Project structure 방법
---

java 폴더 -> Sources Tab 선택  
test 폴더 -> tests Tab 선택  
resource 폴더 -> resource Tab 선택  

<br>

[![intellij-classpath-s1](/assets/img/devlog/201909/intellij-classpath-s1.png)]()


<br>


## 프로젝트 내 폴더 우클릭 > Mark Directory as.. 방법
---

java 폴더 -> Sources Root 클릭  
test 폴더 -> tests Sources Root 클릭  
resource 폴더 -> resource Root 클릭  

[![intellij-classpath-s2](/assets/img/devlog/201909/intellij-classpath-s2.png)]()