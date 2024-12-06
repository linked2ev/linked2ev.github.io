---
layout: post
title:  "[Practical-Java] 10. Parallel Programming-Interface : Runnable, Callable"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Thread, Runnable, Callable, ExecutorService 는 병렬 처리 및 비동기 실행 등 멀티 스레딩에 대해서 지원한다. 해당 포스팅에서는 Runnable과 Callable에 대한 내용이다.  
`Runnable과 Callable`은 Java에서 병렬 또는 비동기 작업을 정의할 때 사용되는 함수형 인터페이스입니다. 함수형 인터페이스이기에 단일 추상 메서드로 Runnable은 run() 메서드를, Callable은 call() 메서드를 정의한다. 람다 표현식과 함께 사용될 수 있어 Java 8 이상에서 함수형 프로그래밍을 지원한다.

<br>


# 병렬 프로그래밍-함수형 인터페이스 : Runnable, Callable

`Runnable`과 `Callable`은 Java에서 비동기 또는 병렬 작업을 정의하는 데 사용되는 위한 `함수형 인터페이스`이다. 이 두 인터페이스는 Java의 `java.util.concurrent` 패키지에 포함되어 있으며, 각각 다른 방식으로 작업을 정의하고 실행 결과를 처리한다.

<br><br>


# 1. Runnable 함수형 인터페이스

`Runnable` 인터페이스는 결과를 반환하지 않는 실행 가능한 작업을 정의하는 데 사용된다.

```java
@FunctionalInterface
public interface Runnable {
    void run();
}
```

<br>

`Runnable`의 `run()` 메서드는 반환 값이 없으며, 예외를 명시적으로 던지지 않는다. 이 인터페이스는 주로 스레드에 작업을 제공할 때 사용되며, `Thread` 클래스의 생성자에 직접 전달하거나, 스레드 풀을 통해 실행할 수 있다.

### Runnable 예제

```java
public class RunnableExample {

    public static void main (String[] args) {
        //Runnable 인터페이스를 익명 클래스로 구현
        Runnable task = new Runnable() {
            @Override
            public void run() {
                System.out.println("Runnable Run Abstract Method Override 구현");
            }
        };
        //람다식
        //Runnable task = () -> System.out.println("Runnable Run Abstract Method Override 구현e");

        Thread thread = new Thread(task);
        thread.start();
    }
}
```

<br><br>


# 2. Callable 함수형 인터페이스

`Callable` 인터페이스는 결과를 반환하고 예외를 던질 수 있는 실행 가능한 작업을 정의하는 데 사용된다.

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

`Callable`의 `call()` 메서드는 작업 실행 후 결과를 반환하며, 필요한 경우 예외를 던질 수 있습니다. `Callable`은 `Runnable`보다 유연하며, 주로 `ExecutorService`와 같은 스레드 풀에서 사용되어 작업의 결과를 `Future` 객체를 통해 조회할 수 있다.

### Callable 예제

```java
public class CallableExample {
    public static void main(String[] args) throws Exception {

        //Callable 인터페이스를 익명 클래스로 구현
        Callable<String> task = new Callable<String>() {
            @Override
            public String call() throws Exception {
                return "Callable Run Abstract Method Override 구현";
            }
        };
        //람다식
        //Callable<String> task = ()-> "Result from Callable";

        ExecutorService executor = Executors.newFixedThreadPool(1);
        Future<String> future = executor.submit(task);

        //Future 객체를 통해 결과
        System.out.println("Callable result: " + future.get());
        executor.shutdown();
    }
}
```

<br><br>

---