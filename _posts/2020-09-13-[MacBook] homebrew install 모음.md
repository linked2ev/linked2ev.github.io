---
layout: post
title:  "[MacBook] homebrew install 모음"
subtitle:   "[MacBook]"
categories: devsub
tags: macbook
comments: true
---


homebrew install 모음

<br><br> 


# Homebrew 설치

> [참고] [MacBook-Mac-OS-패키지관리자-HomeBrew-설치](https://linked2ev.github.io/devsub/2020/06/04/MacBook-Mac-OS-패키지관리자-HomeBrew-설치/)


<br>

> homebrew 설치

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

> cask 설치

```
brew install cask
```

<br>


> open jdk 설치

```
brew tap AdoptOpenJDK/openjdk
brew cask install adoptopenjdk8
-- (# brew cask install <자바 버젼>)
```

> [참고] [Macbook-MacBook에-Open-JDK-설치하기](https://linked2ev.github.io/devsub/2020/06/08/Macbook-MacBook에-Open-JDK-설치하기)

---

<br>

> Git

```
brew install git
```

<br>

> intellij 

- Community Edition
- https://formulae.brew.sh/cask/intellij-idea-ce

```
brew cask install intellij-idea-ce
```

- Ultimate
- https://formulae.brew.sh/cask/intellij-idea-ce

```
brew cask install intellij-idea
```

<br>


> MySql Workbench

```
brew cask install mysqlworkbench
```

<br>


> DBeaver

```
brew cask install dbeaver-community
```

<br>


> node.js / npm

```
brew install node
npm install
```

<br>


## Client Terminal

> zsh

```
# zsh 설치하기
brew install zsh

# zsh 설치경로 확인하기
which zsh

# 기본 shell 변경하기
chsh -s $(which zsh)
```

<br>


> iterm2

```
brew cask install iterm2
```



## Etc

> chrome

```
brew cask install google-chrome
```

> evernote

```
brew cask install evernote
```

> sourcetree

```
brew cask install source-tree
```

> bootsonte

```
brew cask install visual-studio-code
-- bootsnote, atom
```


<br>

---

