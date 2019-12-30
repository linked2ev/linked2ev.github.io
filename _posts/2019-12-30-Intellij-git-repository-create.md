---
layout: post
title:  "[IntelliJ] Git 연동 후 프로젝트 원격저장소 초기 생성 및 복사"
subtitle:   "[IntelliJ]"
categories: devsub
tags: intellij
comments: true
---

Windows10 OS 환경에서 IntelliJ에 Git 연동 후 프로젝트 원격저장소에 초기 생성(Share)하고 로컬저장소에 복사(Clone)하기

<br><br>


# 1. Intellij Git 환경설정 및 연동하기

우선 git은 설치되었다는 가정하에, Intellij에 Git을 설정을 진행하겠다.

<h3>1) File > Setting (Ctrl+Alt+S)</h3>

[![intellij-git-share-s1.png](/assets/img/devlog/201912/intellij-git-share-s1.png)]()

<br><br>

<h3>2) Git 메뉴에서 git 실행경로에 git.exe 실행파일을 선택후에 TEST를 해서 확인하다.</h3>

[![intellij-git-share-s2.png](/assets/img/devlog/201912/intellij-git-share-s2.png)]()

<br><br>


<h3>3) Github 메뉴에서 Add Account를 클릭해서 git계정을 추가하면 된다.</h3>
추가하면 추가 된 github계정 목록이 나온다.

[![intellij-git-share-s3.png](/assets/img/devlog/201912/intellij-git-share-s3.png)]()

<br><br>


이렇게 githun 환경설정은 끝났고 이어서 intellij에서 생성 된 프로젝트를 바로 git remote repository를 생성하겠다.

<br><br>


# 2. Intellij에서 프로젝트 Git Remote Repository에 생성하기

생성 된 프로젝트를 git remote repository(원격 저장소)에 올려서 팀에 Share(공유)하는 개념이다.

[![intellij-git-share-s4.png](/assets/img/devlog/201912/intellij-git-share-s4.png)]()


<br><br>

[![intellij-git-share-s5.png](/assets/img/devlog/201912/intellij-git-share-s5.png)]()

<br><br>

[![intellij-git-share-s6.png](/assets/img/devlog/201912/intellij-git-share-s6.png)]()

<br><br>

이와 같이 프로젝트를 Intellij에서 올리면 github에 저장소가 생긴 것을 확인 할 수가 있다.

[![intellij-git-share-s7.png](/assets/img/devlog/201912/intellij-git-share-s7.png)]()


<br><br>

# 3. Intellij에서 프로젝트 Git Clone 받기

이제는 Git 원격저장소에 있는 프로젝트 Clone하면 해당 디렉터리에 git local repository가 생성된다.

[![intellij-git-clone-s1.png](/assets/img/devlog/201912/intellij-git-clone-s1.png)]()


[![intellij-git-clone-s2.png](/assets/img/devlog/201912/intellij-git-clone-s2.png)]()


<br><br>

빠른 시일내로 Intellij로 git 작업 관련을 포스팅 할 예정이다.