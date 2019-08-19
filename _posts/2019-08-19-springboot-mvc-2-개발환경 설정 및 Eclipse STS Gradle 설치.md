---
layout: post
title:  "2. 개발환경 설정 및 Eclipse STS Gradle 설치"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-mvc
comments: true
---

개발하기 앞서 환경 설정과 Eclipse에 STS Gradle 설치를 한다.

<br>

---

> 개발 환경  

- OS: Window 10
- Dev Tool: Eclipse IDE 2019-06
- JDK: 1.8(_221)
- Framework: Springboot
- Build Tool: Gradle

---

<br>


# 1. 개발 환경 설정

개발 툴은 Eclipse로 진행한다. Eclipse 버전은 무방 할 듯 하다.  

<br>

## Eclipse 다운로드
---

- 최근에 사용하는 이클립스 버젼이기에 본인은 해당 버젼으로 다운로드했다.
- https://www.eclipse.org/downloads/packages/release/2019-03/r

[![sts-mvc-2-01](/assets/img/devlog/201908/sts-mvc-2-01.png)]()

<br>


## JDK 1.8 다운로드
---

- OpenJDK는 기억이 가물가물하지만 SHA512 관련해서 클래스가 없었던 기억이 있기에 그냥 안전하게 JDK 1.8로 진해
- https://www.oracle.com/technetwork/java/javase/downloads/index.html

<br>

[![sts-mvc-2-02](/assets/img/devlog/201908/sts-mvc-2-02.png)]()

[![sts-mvc-2-03](/assets/img/devlog/201908/sts-mvc-2-03.png)]()

[![sts-mvc-2-04](/assets/img/devlog/201908/sts-mvc-2-04.png)]()

<br>


## project 디렉토리 구조 설정
---

- 본인은 C:\project 경로에 project명으로 "neo"라는 폴더를 생성했다.
- 다운 받은 Eclipse는 압축을 해제하고 eclipse폴더를   
JDK는 설치를 다하고 C:\Program Files 경로에 Java폴더를  
새로 생성한 C:\project\neo 로 가져온다.
- 그리고 workspace 폴더를 생성해준다.

[![sts-mvc-2-05](/assets/img/devlog/201908/sts-mvc-2-05.png)]()

<br>


## ecplise.ini 파일 설정
---

- C:\project\neo\eclipse\ecplise.ini 파일을 열어서 jdk경로와 힙 메모리를 설정해준다.

```xml
<!-- 기존 ecplise.ini 부분 -->
openFile
--launcher.defaultAction
openFile
--launcher.appendVmargs
-vmargs
-Dosgi.requiredJavaVersion=1.8
-Dosgi.instance.area.default=@user.home/eclipse-workspace
-XX:+UseG1GC
-XX:+UseStringDeduplication
--add-modules=ALL-SYSTEM
-Dosgi.requiredJavaVersion=1.8
-Dosgi.dataAreaRequiresExplicitInit=true
-Xms256m
-Xmx1024m
--add-modules=ALL-SYSTEM
```

```xml
<!-- 수정 ecplise.ini 부분 -->
openFile
--launcher.defaultAction
openFile
--launcher.appendVmargs
-vm
C:/project/neo/Java/jdk1.8.0_221/bin/javaw.exe
-vmargs
-Dosgi.requiredJavaVersion=1.8
-Dosgi.instance.area.default=@user.home/eclipse-workspace
-XX:+UseG1GC
-XX:+UseStringDeduplication
--add-modules=ALL-SYSTEM
-Dosgi.requiredJavaVersion=1.8
-Dosgi.dataAreaRequiresExplicitInit=true
-Xms1024m
-Xmx2048m
--add-modules=ALL-SYSTEM
```

<br>


## Ecplise 실행
---

- Ecplise를 실행하면 이전에 생성한 workspace로 설정하고 진행하면 된다.

[![sts-mvc-2-06](/assets/img/devlog/201908/sts-mvc-2-06.png)]()


<br><br>

# 2. Eclipse 개발 환경 설정

- 이클립스를 실행하면 이와 같은 화면이다.

[![sts-mvc-2-07](/assets/img/devlog/201908/sts-mvc-2-07.png)]()

<br>


## Ecplise Marketplace

- Ecplise 상단에 Help > Ecplise Marketplace 를 통해서 외부프로그램을 설치한다.

> STS 설치

[![sts-mvc-2-08](/assets/img/devlog/201908/sts-mvc-2-08.png)]()

<br>

> Gradle 설치

- 해당 툴은 기본적으로 gradle를 지원한다.

[![sts-mvc-2-09](/assets/img/devlog/201908/sts-mvc-2-09.png)]()

<br>

> Gradle Editor 설치

[![sts-mvc-2-10](/assets/img/devlog/201908/sts-mvc-2-10.png)]()


<br>

---
참고