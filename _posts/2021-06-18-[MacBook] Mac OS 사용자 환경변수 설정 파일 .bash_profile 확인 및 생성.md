---
layout: post
title:  "[MacBook] Mac OS 사용자 환경변수 설정 파일 .bash_profile 확인 및 생성"
subtitle:   "[MacBook]"
categories: devsub
tags: macbook
comments: true
---


Mac에서 사용자 환경변수 설정 파일인 `.bash_profile` 확인

<br><br>


## Mac에서 환경변수 PATH 설정 파일인 `.bash_profile` 확인

<br>

> `참고`

명령어 | 의미 | 설명
--- | --- | ---
touch | Touch | 파일 생성
echo | Echo | 파일에 텍스트 추가
ls -al | List | 숨겨진 파일까지 모두 상세 내역을 목록 조회

<br><br>


아래 해당 디렉터리에서 파일 존재 유무 확인

```
$ cd ~/Users/{맥북 사용자 이름}
```

```
$ ls -al
```

<br><br>


`.bash_profile` 파일이 없는 경우 해당 경로에 생성하면 된다. 

```
~/Users/{맥북 사용자 이름}/.bash_profile
```

```
$ touch .bash_profile
$ ls -al
``` 

생성 된 .bash_profile 파일 확인

