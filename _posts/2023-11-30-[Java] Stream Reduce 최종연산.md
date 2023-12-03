---
layout: post
title:  "[Java] Stream Reduce 최종연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.reduce()`는 스트림의 모든 요소를 하나로 줄이는 연산을 수행합니다. 이 연산은 각 요소를 조합하여 최종 결과를 생성하는 데 사용된다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream Reduce 예제

```java
Optional<T> reduce(BinaryOperator<T> accumulator)
T reduce(T identity, BinaryOperator<T> accumulator)
U reduce(U identity, BiFunction<U,T,U> accumulator, BinaryOperator<U> combiner)
```

- identity : 초기값
- accumulator : 이전 연산결과와 스트림의 요소에 수행할 연산
- combiner : 병렬처리된 결과를 합치는데 사용할 연산(병렬 스트림) 


```java
public class ExStreamReduce {

    public static void main(String[] args) {

        List<Integer> numbers = Arrays.asList(363, 538, 749, 121, 213, 125, 11, 569, 387, 921);

        //T reduce(T identity, BinaryOperator<T> accumulator)
        int count = numbers.stream().reduce(0, (a, b) -> a+1);
        System.out.println("# count : " + count);

        int sum = numbers.stream().reduce(0, (a, b) -> a+b);
        System.out.println("# sum : " + sum);

        int min = numbers.stream().reduce(Integer.MAX_VALUE, (a, b) -> a<b ? a:b);
        System.out.println("# min : " + min);

        int max = numbers.stream().reduce(Integer.MIN_VALUE, (a, b) -> a>b ? a:b);
        System.out.println("# max : " + max);

        //Optional<T> reduce(BinaryOperator<T> accumulator)
        Optional<Integer> optMin = numbers.stream().reduce(Integer::min);
        System.out.println("# optMin : " + optMin.get());

        Optional<Integer> optMax = numbers.stream().reduce(Integer::max);
        System.out.println("# optMax : " + optMax.get());
    }
}
```

```
# count : 10
# sum : 3997
# min : 11
# max : 921
# optMin : 11
# optMax : 921
```