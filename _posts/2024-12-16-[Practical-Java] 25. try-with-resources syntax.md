---
layout: post
title:  "[Practical-Java] 25. try-with-resources syntax"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

`try` 구문은 자바에서 예외 처리를 위해 사용되며, 특히 `try-with-resources` 구문은 자원을 사용한 후에 자동으로 닫아주는 기능을 제공한다. 이 구문은 자바 7부터 지원되며, 파일 입출력이나 데이터베이스 연결과 같이 사용이 끝난 후에 닫아야 하는 자원을 다룰 때 유용하게 사용된다.

<br><br>


# 1. try-with-resources 구문

### 기본 형태

`try-with-resources` 구문의 기본 형태는 다음과 같다:

```java
try (ResourceType resource = new Resource()) {
    // 자원을 사용하는 코드
} catch (ExceptionType e) {
    // 예외 처리 코드
} finally {
    // 자원을 정리하는 코드, try-with-resources를 사용하면 필요 없다
}
```

### 작동 원리

- **자원의 초기화와 선언**: `try` 괄호 안에서 자원을 초기화하고 선언한다. 이 자원은 `java.lang.AutoCloseable` 인터페이스 또는 `java.io.Closeable` 인터페이스를 구현해야 한다. 이 괄호 안에 선언된 모든 자원은 `try` 블록이 종료될 때 자동으로 `close()` 메서드가 호출되어 자원이 안전하게 닫힌다.
- **예외 처리**: 자원을 사용하는 동안 예외가 발생하면 `catch` 블록에서 이를 잡아 처리할 수 있다. `try-with-resources` 구문은 자원에서 발생할 수 있는 예외뿐만 아니라 `try` 블록 내 코드에서 발생하는 예외도 잡을 수 있다.
- **자원의 자동 종료**: `try` 블록의 코드 실행이 끝나면, 선언된 자원의 `close()` 메서드가 자동으로 호출된다. 이는 코드에서 명시적으로 자원을 닫는 것을 잊었을 때 발생할 수 있는 자원 누수 문제를 예방한다.

### 예제 코드

다음은 `try-with-resources` 구문을 사용한 파일 읽기 예제이다:

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class Main {
    public static void main(String[] args) {
        String line;
        try (BufferedReader reader = new BufferedReader(new FileReader("example.txt"))) {
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
}
```

이 예제에서 `BufferedReader`는 `AutoCloseable` 인터페이스를 구현하므로 `try` 블록이 종료되면 자동으로 `close()` 메서드가 호출되어 파일 리더가 안전하게 닫힌다.

<br><br>


