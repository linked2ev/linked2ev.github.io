---
layout: post
title:  "[Java] Stream Match, Find 최종연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream에 조건 검사 allMath(), anyMatch(), noneMatch() 와 findFirst(), findAny()` 최종연산에 대한 포스팅이다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream allMath(), anyMatch(), noneMatch() 예제

Predicate를 매개변수로 받아 스트림의 요소들에 대해 조건을 적용하며, 해당 조건을 만족하는지 여부를 판단합니다. 이들은 스트림 처리 시 각 요소에 대한 조건을 검사하여 불린(boolean) 값을 반환한다.

```java
boolean allMatch(Predicate<? super T> predicate)      //모든 요소를 만족하는지 확인
boolean anyMatch(Predicate<? super T> predicate)      //하나라도 요소를 만족하는지 확인
boolean noneMatch(Predicate<? super T> predicate)     //모든 요소를 만족시키지 않는지 확인
```

1. **`allMatch()`**: 이 메서드는 스트림의 모든 요소가 주어진 조건(Predicate)을 만족하는지 여부를 확인합니다. 모든 요소가 조건을 만족하면 `true`를 반환하고, 그렇지 않으면 `false`를 반환합니다.

2. **`anyMatch()`**: 이 메서드는 스트림의 요소 중 하나라도 주어진 조건을 만족하는지 여부를 확인합니다. 하나 이상의 요소가 조건을 만족하면 `true`를 반환하고, 모든 요소가 조건을 만족하지 않으면 `false`를 반환합니다.

3. **`noneMatch()`**: 이 메서드는 스트림의 모든 요소가 주어진 조건을 만족하지 않는지 여부를 확인합니다. 모든 요소가 조건을 만족하지 않으면 `true`를 반환하고, 하나라도 조건을 만족하면 `false`를 반환합니다.

<br>

```java
public class ExStreamMatch {

    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 3, 5, 7, 9);

        boolean allPositive = numbers.stream().allMatch(num -> num > 0);
        System.out.println("모든 요소가 양수인가요? " + allPositive);

        boolean anyNegative = numbers.stream().anyMatch(num -> num < 0);
        System.out.println("하나라도 음수인 요소가 있나요? " + anyNegative);

        boolean noneZero = numbers.stream().noneMatch(num -> num == 0);
        System.out.println("모든 요소가 0이 아닌가요? " + noneZero);
    }
}
```

```
모든 요소가 양수인가요? true
하나라도 음수인 요소가 있나요? false
모든 요소가 0이 아닌가요? true
```

<br><br>


# Stream findFirst(), findAny() 예제

두 메서드 모두 `Optional`을 반환하는데, 이는 스트림에서 요소를 찾지 못했을 경우에 대해서이다. `findFirst()`와 `findAny()`는 요소를 찾으면 해당 요소를 담은 `Optional`을 반환하고, 요소가 없는 경우에는 `Optional.empty()`를 반환한다.

```java
Optional<T> findFirst()         //첫번째 요소를 반환, 순차 스트림에 사용
Optional<T> findAny()           //아무 요소 하나를 반환, 병렬 스트림에 사용
```

1. **`findFirst()`**: 이 메서드는 순차적으로 처리되는 스트림에서 첫 번째 요소를 찾아 반환합니다. 스트림의 첫 번째 요소를 찾아서 그 값을 `Optional` 객체에 담아 반환합니다. 만약 스트림이 비어있다면, `Optional.empty()`를 반환합니다.

2. **`findAny()`**: 이 메서드는 병렬 처리되는 스트림에서 어떤 요소든 하나를 찾아 반환합니다. 병렬로 처리되는 경우에는 어떤 요소든 찾을 수 있습니다. 주의할 점은 순차 스트림과 병렬 스트림의 경우 동일한 결과를 보장하지 않습니다. 비어있는 스트림에서 호출된 경우에도 `Optional.empty()`를 반환합니다.

<br>

```java
public class ExStreamFind {

    public static void main(String[] args) {

        List<AccSubItem> AccSubItems = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("B222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("A610", "20", new BigDecimal(431000)),
                new AccSubItem("A105", "10", new BigDecimal(12346)),
                new AccSubItem("B673", "10", new BigDecimal(100000)),
                new AccSubItem("B213", "10", new BigDecimal(120000)),
                new AccSubItem("A444", "20", new BigDecimal(1000)),
                new AccSubItem("B345", "10", new BigDecimal(27000)),
                new AccSubItem("C896", "10", new BigDecimal(431000)),
                new AccSubItem("C567", "10", new BigDecimal(12346))
        );

        Optional<AccSubItem> retFirst = AccSubItems.stream()
                                                .filter(e -> e.getAccId().startsWith("B"))
                                                .findFirst();
        System.out.println("# findFirst = " + retFirst.get());   //순차적으로 filter 조건에 맞는 "B222"만 반환

        Optional<AccSubItem> retAny = AccSubItems.parallelStream()
                                                .filter(e -> e.getAccId().startsWith("B"))
                                                .findAny();
        System.out.println("# retAny = " + retAny.get());        //병렬처리시에 filter 조건에 맞는 아무 쓰레드에서 반환
    }
}
```


```
# findFirst = AccSubItem{accId='B222', depWdrTypCd='20', accAmt=1000}
# retAny = AccSubItem{accId='B213', depWdrTypCd='10', accAmt=120000}
```

