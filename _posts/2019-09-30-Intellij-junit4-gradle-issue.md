---
layout: post
title:  "[IntelliJ] JUnit4 + Gradle - update broke all tests with error: no tests found for given includes"
subtitle:   "[IntelliJ]"
categories: devsub
tags: intellij
comments: true
---

IntelliJ JUnit4 + Gradle를 이용해서 test code 작성 시 발생한 이슈이다

<br><br>


# ISSUE LOG

```
update broke all tests with error: no tests found for given includes [xxxx.someThingTest](filter.includetestsmatching)
```

<br>


# ISSUE 해결

1. File > Setting (Ctrl+Alt+S)
2. Build, Execution, Deployment > Build Tools > gradle
3. Run Tests using: `Intellij IDEA`

<br>


[![intellij-gradle-junit-issue-s1](/assets/img/devlog/201909/intellij-gradle-junit-issue-s1.png)]()

<br><br>

> 만약, JUnit5 일 경우

```yml
test {
    useJUnitPlatform()
}
```