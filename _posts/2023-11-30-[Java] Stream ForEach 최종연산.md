---
layout: post
title:  "[Java] Stream ForEach 최종연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.forEach()`은 중첩된 구조의 요소들을 평면화(flatten)하는 데 사용된다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream ForEach 예제

- void forEach(Consumer<? super T> action) : 직렬로 모든 요소에 대해 지정 된 작업을 수행
- void forEachOrdered(Consumer<? super T> action) : 모든 요소에 대해 순서를 보장 된 작업을 수행, 병렬 스트림 경우 순서가 보장

<br>

```java
public class ExStreamForEach {

    public static void main(String[] args) {

        //기본 sequential() 직렬 스트림
        IntStream.range(1, 10).sequential().forEach(System.out::print);
        System.out.println("");
        IntStream.range(1, 10).sequential().forEachOrdered(System.out::print);
        System.out.println("");

        //parallel() 병렬 스트림
        //- forEach : 병렬 스트림 경우 순서가 보장되지 않음
        IntStream.range(1, 10).parallel().forEach(System.out::print);
        System.out.println("");
        //- forEachOrdered : 병렬 스트림 경우 순서가 보장됨
        IntStream.range(1, 10).parallel().forEachOrdered(System.out::print);
        System.out.println("");
    }
}

```

```
123456789
123456789
657123498
123456789
```