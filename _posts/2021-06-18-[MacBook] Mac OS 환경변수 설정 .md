---
layout: post
title:  "[MacBook] Mac OS 환경변수 설정"
subtitle:   "[MacBook]"
categories: devsub
tags: macbook
comments: true
---

Mac OS 에서 사용자 환경변수 설정

<br><br>


## Mac OS 사용자 환경변수 설정

> .bash_profile

사용자 User의 개인 환경 설정 파일이다. 
즉 시스템 전역 설정이 아니고User가 로그인 할 때마다 실행되는 파일이다.

```
~/Users/{맥북 사용자 이름}/.bash_profile
```

<br>

> .bash_profile 사용자 환경 변수 설정

```
$ echo "export PATH=$PATH:/Users/linked2ev/bin" >> .bash_profile
$ source .bash_profile
$ vi .bash_profile
```

`echo` 명령어를 통해 파일에 텍스트 추가 한 후  
`source` 명령어를 통해 .bash_profile 파일 설정을 적용  
`vi` 명령어로 .bash_profile 설정 확인

<br><br>


## Mac OS 시스템 전역 환경변수 설정

```
$ echo $PATH
$ export PATH=$PATH:/Users/linked2ev/bin            
$ echo $PATH  
```