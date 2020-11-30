---
layout: post
title:  "[IntelliJ] Git 사용 - 커밋, 브랜치 생성, STASH"
subtitle:   "[IntelliJ]"
categories: devsub
tags: intellij
comments: true
---

Git 커밋, 브랜치 생성

<br><br>


# COMMIT & PUSH (커밋 & 푸시)


```
$ git add -all
$ git commit -m "#intellij first commit"
$ git push origin 
```

<br>

[![idea-git-commit-s01](/assets/img/2020/11/idea-git-commit-s01.png)]() <br>

[![idea-git-commit-s02](/assets/img/2020/11/idea-git-commit-s02.png)]() <br>

<br><br>


# BRANCH (브랜치 생성 & 원격 저장소 브랜치 생성)

```
$ git branch feature/test
$ git checkout feature/test
$ git push --set-upstream origin feature/test
```

<br>

[![idea-git-branch-s01](/assets/img/2020/11/idea-git-branch-s01.png)]() <br>

[![idea-git-branch-s02](/assets/img/2020/11/idea-git-branch-s02.png)]() <br>

[![idea-git-branch-s03](/assets/img/2020/11/idea-git-branch-s03.png)]() <br>

[![idea-git-branch-s04](/assets/img/2020/11/idea-git-branch-s04.png)]() <br>

[![idea-git-branch-s06](/assets/img/2020/11/idea-git-branch-s06.png)]() <br>

[![idea-git-branch-s07](/assets/img/2020/11/idea-git-branch-s07.png)]() <br>

<br><br>


# CHEKCOUT (브랜치 이동)

```
$ git checkout develop
```

[![idea-git-checkout-s01](/assets/img/2020/11/idea-git-checkout-s01.png)]() <br>


[![idea-git-checkout-s02](/assets/img/2020/11/idea-git-checkout-s02.png)]() <br>


<br><br>


# STASH (임시저장소 생성)

아직 마무리하지 않은 작업을 스택에 잠시 저장할 수 있도록 하는 명령어이다. 이를 통해 아직 완료하지 않은 일을 commit하지 않고 나중에 다시 꺼내와 마무리할 수 있다.

git stash 명령을 사용하면 워킹 디렉토리에서 수정한 파일들만 저장한다.
stash란 아래에 해당하는 파일들을 보관해두는 장소 이다.


```
$ git stash save  "stash test"
```

<br>

[![idea-git-stash-s01](/assets/img/2020/11/idea-git-stash-s01.png)]() <br>

[![idea-git-stash-s02](/assets/img/2020/11/idea-git-stash-s02.png)]() <br>

[![idea-git-stash-s03](/assets/img/2020/11/idea-git-stash-s03.png)]() <br>

<br><br>


# UNSTASH (임시저장소 불러오기)

```
$ git stash list
$ git stash apply stash@{숫자} -- 리스트 출력 index
```

```
$ git stash clear -- 스태시 삭제
```

<br>

[![idea-git-unstash-s01](/assets/img/2020/11/idea-git-unstash-s01.png)]() <br>

[![idea-git-unstash-s02](/assets/img/2020/11/idea-git-unstash-s02.png)]() <br>

<br><br>

