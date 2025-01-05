---
layout: post
title:  "[Practical-Java] 24. JShell"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바9에서는 CLI 환경에서 코드를 작성하고 테스트를 할 수 있는 기능이 추가되었는데 이를 `JShell`이라고 한다. 아래와 같이 터미널에서 개발코드 작성이 가능하고 단순한 코드에 대해서 테스트할만 하지만 실제 프로젝트시에는 테스트 도구가 많아서 굳이 사용해본적은 없다. 정상적인 환경에서는 쓸일이 없을 것 같다.

<br><br>


# 1. JShell 의 정의

JShell은 Java 프로그래밍 언어를 위한 대화형 쉘 환경으로, Java 9 버전에서 처음 도입되었다. 이 도구는 개발자들이 실시간으로 코드 조각을 실행하고 결과를 볼 수 있게 하며, 특히 학습이나 실험, 디버깅 과정에서 유용하다. JShell은 REPL(Read-Eval-Print Loop)을 구현하여, 코드를 작성하고 즉시 평가하며 결과를 확인할 수 있다.

```shell
jshell> String a = "test";
a ==> "test"
|  created variable a : String

jshell> a.contains("est")
$3 ==> true
|  created scratch variable $3 : boolean
```

<br><br>


# 2. 코드 스니핏

스니핏(snippet)은 작은 코드 조각을 의미한다. 일반적으로 스니핏은 특정 작업을 수행하는 데 필요한 충분한 코드를 포함하고 있으며, 프로그래밍에서는 반복적인 작업을 빠르게 수행할 수 있도록 도와주는 짧은 코드 블록이다.  
JShell 에서 개발자가 작성한 코드 조각을 아래와 같은 명령들로 확인이 가능하다.

### 2-1. JShell 주요 명령어

- `/list`: 지금까지 실행한 모든 코드 조각을 보여준다.
- `/exit`: JShell 세션을 종료한다.
- `/reset`: JShell 세션을 초기화한다, 모든 변수, 메소드, 클래스 정의를 삭제한다.
- `/vars`: 현재 세션에서 정의된 모든 변수를 보여준다.
- `/methods`: 현재 세션에서 정의된 모든 메소드를 보여준다.
- `/classes`: 현재 세션에서 정의된 모든 클래스를 보여준다.
- `/imports`: 현재 세션에 포함된 모든 import 문을 보여준다.
- `/help`: JShell 도구의 사용법과 명령어 목록을 제공한다.
- `/save [filename]`: 현재 세션의 내용을 파일로 저장한다.
- `/open [filename]`: 파일에서 JShell 세션을 로드한다.

```JShell
jshell> /list

   1 : int age = 46;
   3 : a.contains("est")
   4 : BigDecimal a = new BigDecimal("123");
```

<br>


### 2-2. 외부 편집기

- `/edit` : 편집기를 사용하여 JShell의 기본 편집기를 사용하거나, 외부 편집기를 설정하여 사용할 수 있다.

<br><br>


# 3. 외부 클래스 연동

JShell에서 외부 클래스 파일이나 JAR 파일을 추가하고 싶을 때 사용할 수 있는 방법을 설명하겠습니다. JShell은 실행 시 또는 실행 중에 클래스패스를 설정하여 외부 라이브러리나 클래스 파일을 로드할 수 있습니다.

### 3-1. JShell 클래스패스 설정

JShell을 시작할 때 클래스패스를 명시적으로 설정하여 외부 JAR 파일이나 클래스를 로드하는 방법이다.

- **JAR 파일을 포함한 클래스패스로 JShell 시작**:

```bash
jshell --class-path "/path/to/your/library.jar"
```


### 3-2. JShell 클래스패스 설정 변경

JShell 세션 중에 클래스패스를 변경하고 싶다면 `/env` 명령을 사용할 수 있다.

- **클래스패스 추가**:

```java
/env -class-path "/path/to/your/library.jar"
```


### 3-3. JShell 모듈path 설정

JShell을 시작할 때 지정된 경로의 모듈을 모듈path 추가하고, `--add-modules` 옵션으로 특정 모듈을 사용하도록 설정한다.

- **모듈패스로 JShell 시작**:

```bash
jshell --module-path "/path/to/your/modules" --add-modules module.name
```


### 3-4. JShell 모듈Path 설정 변경

- **모듈 정보 조회**:

```java
/env
```

<br><br>

아래는 JShell에서 한 번에 입력할 수 있는 코드 블록 형태로 JShell에 작성하면 정상 실행되는 것을 볼 수있다.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;

HttpClient client = HttpClient.newHttpClient();

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://jsonplaceholder.typicode.com/users/1"))
    .GET()
    .build();

HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
System.out.println(response.body());
```

<br><br>


