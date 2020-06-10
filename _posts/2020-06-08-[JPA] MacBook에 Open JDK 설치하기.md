---
layout: post
title:  "[JPA] MacBook에 Open JDK 설치하기"
subtitle:  "[MacBook]"
categories: JPA
tags: jpa
comments: true
---


MacBook 에 JDK 설치를 위해 OpenJDK를 설치하는 포스팅이다.

<br><br>


# Mac 에 OpenJDK 설치


> OpenJDK - https://openjdk.java.net/

Java 애플리케이션을 실행하기 위해서는 JVM이 필요하고 컴파일하기 위해서는 JDK가 필요하다. 일반적으로 JDK를 설치하면 JVM도 같이 설치된다. JDK는 2개 버전으로 나뉘는데 하나는 상업 코드 기반의 Oracle JDK이고 하나는 오픈 소스 기반의 OpenJDK이다. 해당 포스팅에서는 무료로 많이 쓰이는 OpenJDK로 설치 할 예정이다.

<br>


> AdoptOpenJDK - https://adoptopenjdk.net/

AdoptOpenJDK는 사전에 prebuild 형태로 java binary를 제공하는 커뮤니티 그룹이다. 홈페이지 Mac 뿐만 아니라 윈도우, 리눅스 환경도 제공하고 있다. 공식적으로 OpenJDK를 설치하는건 직접 빌드해서 사용하는 방법이 있지만, 빌드 이외에도 자잘한 JAVA_HOME (빌드해서 사용했더니 “/usr/libexec/java_home” 이 동작하지 않았다..) 설정 문제라던가 버전업을 편하게 하기 위해서 homebrew를 사용해서 AdoptOpenJDK를 설치하도록 했다.


github - https://github.com/AdoptOpenJDK/homebrew-openjdk

---

<br>


그 전에 자신의 맥북에 Mac OS 패키지관리자 HomeBrew가 설치되어 있으면 쉽게 OpenJDK 를 설치 할 수 있다.

HomeBrew 가 없으면 [https://linked2ev.github.io/devsub/2020/06/04/MacBook-Mac-OS-패키지관리자-HomeBrew-설치/](https://linked2ev.github.io/devsub/2020/06/04/MacBook-Mac-OS-패키지관리자-HomeBrew-설치/) 에 HomeBrew 설치 포스팅을 참조하면 된다.

<br>

- OpenJDK8 - adoptopenjdk8
- OpenJDK9 - adoptopenjdk9
- OpenJDK10 - adoptopenjdk10
- OpenJDK11 - adoptopenjdk11
- OpenJDK11 - OpenJ9 JVM - adoptopenjdk11-openj9

<br>

```
brew tap AdoptOpenJDK/openjdk
brew cask install adoptopenjdk8
# brew cask install <자바 버젼>
```

`! 만약 삭제 후에 같은 버젼 재설치 할 경우는 "reinstall" 로 명어로 실행하면 된다.`

```
brew cask reinstall adoptopenjdk8
```

위와 같이 중간에 패스워드를 입력하면 정상적으로 설치 된다.

정말로 설치가 되었는지 확인해보자. 자바 버젼울 명령어로 확인해보고

```
java -version
```

<br>

자바 설치 경로 파일로 이동해서 확인이 가능하다

```
scd /Library/Java/JavaVirtualMachines
```

<br><br>


# Mac 에서 JDK 삭제하기


- https://www.java.com/ko/download/help/mac_uninstall_java.xml
- https://installvirtual.com/uninstall-java-8-mac-os-x/
- https://installvirtual.com/uninstall-java-11-mac/

<br>

Homebrew 를 통해 OpenJDK8 를 설치했으면 아래 명령어로 설치 된 JAVA를 삭제 할 수 있다.

```
brew cask remove adoptopenjdk8
# brew cask remove <JAVA명>
```

<br>

또는 

<br>


> Java를 제거할 때 /usr/bin에서 Java 툴을 제거하지 마십시오. 이 디렉토리는 시스템 소프트웨어의 일부이며, 다음 번에 OS 업데이트를 수행할 때 Apple에서 모든 변경사항을 재설정합니다.

```
sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin 
sudo rm -fr /Library/PreferencePanes/JavaControlPanel.prefPane 
sudo rm -fr ~/Library/Application\ Support/Oracle/Java
```

<br>

JDK를 같이 설치하면 JDK로 시작하는 디렉토리 이름을 확인한 후 해당 파일을 삭제해야 한다.

```
cd /Library/Java/JavaVirtualMachines/
ls
sudo rm -rf /Library/Java/JavaVirtualMachines/*-8*.jdk
# sudo rm -rf /Library/Java/JavaVirtualMachines/[자바 버전].jdk
```






---
[참고]
- https://findstar.pe.kr/2019/01/20/install-openjdk-by-homebrew/
