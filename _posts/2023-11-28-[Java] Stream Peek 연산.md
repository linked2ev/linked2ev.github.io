---
layout: post
title:  "[Java] Stream Peek 연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.peek()` 메서드는 `중간 연산자로 스트림의 요소를 확인하거나 디버깅 목적으로 사용`됩니다. peek() 메서드를 사용하여 스트림의 각 요소를 처리하고, 처리된 요소를 그대로 반환합니다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>

# Stream Peek 예제

```java
Stream<T> peek(Consumer<? super T> action); //중간연산 (스트림 소비 X)
void forEach(Consumer<? super T> action); //최종연산 (스트림 소비 O)
```

```java
public class ExStreamPeek {

    public static void main(String[] args) {
        List<AccSubItem> AccSubItems = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("A222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("A610", "20", new BigDecimal(431000)),
                new AccSubItem("A105", "10", new BigDecimal(12346)),
                new AccSubItem("B673", "10", new BigDecimal(100000)),
                new AccSubItem("B213", "10", new BigDecimal(120000)),
                new AccSubItem("A444", "20", new BigDecimal(1000)),
                new AccSubItem("A345", "10", new BigDecimal(27000)),
                new AccSubItem("C896", "10", new BigDecimal(431000)),
                new AccSubItem("C567", "10", new BigDecimal(12346))
        );

        //2. 객체 내 특정 값 AccId 만 map 을 이용해 변환 replaceAll
        List<Integer> list = AccSubItems.stream()
                                        .map(AccSubItem::getAccId)
                                        .peek(e -> System.out.println("[STEP-1] AccId: " + e))
                                        .filter(e -> e.contains("A"))
                                        .peek(e -> System.out.println("  [STEP-2] A시작 Filter: " +e))
                                        .map(e -> e.replaceAll("A", ""))
                                        .map(Integer::parseInt)
                                        .peek(e -> System.out.println("    [STEP-3] Replace 후 Integer 변환: " + e))
                                        .filter(e -> e < 400)
                                        .peek(e -> System.out.println("      [STEP-4] 400 이하 Filter: " + e))
                                        .sorted()
                                        .peek(e -> System.out.println("[ORDER] Sorted: " + e))
                                        .collect(Collectors.toList());

        System.out.println("list : " + list);
    }
}
```

```
[STEP-1] AccId: A123
  [STEP-2] A시작 Filter: A123
    [STEP-3] Replace 후 Integer 변환: 123
      [STEP-4] 400 이하 Filter: 123
[STEP-1] AccId: A201
  [STEP-2] A시작 Filter: A201
    [STEP-3] Replace 후 Integer 변환: 201
      [STEP-4] 400 이하 Filter: 201
[STEP-1] AccId: A222
  [STEP-2] A시작 Filter: A222
    [STEP-3] Replace 후 Integer 변환: 222
      [STEP-4] 400 이하 Filter: 222
[STEP-1] AccId: A510
  [STEP-2] A시작 Filter: A510
    [STEP-3] Replace 후 Integer 변환: 510
[STEP-1] AccId: A610
  [STEP-2] A시작 Filter: A610
    [STEP-3] Replace 후 Integer 변환: 610
[STEP-1] AccId: A105
  [STEP-2] A시작 Filter: A105
    [STEP-3] Replace 후 Integer 변환: 105
      [STEP-4] 400 이하 Filter: 105
[STEP-1] AccId: B673
[STEP-1] AccId: B213
[STEP-1] AccId: A444
  [STEP-2] A시작 Filter: A444
    [STEP-3] Replace 후 Integer 변환: 444
[STEP-1] AccId: A345
  [STEP-2] A시작 Filter: A345
    [STEP-3] Replace 후 Integer 변환: 345
      [STEP-4] 400 이하 Filter: 345
[STEP-1] AccId: C896
[STEP-1] AccId: C567
[ORDER] Sorted: 105
[ORDER] Sorted: 123
[ORDER] Sorted: 201
[ORDER] Sorted: 222
[ORDER] Sorted: 345
list : [105, 123, 201, 222, 345]
```

<br>

> AccSubItem 클래스 일부

`Comparable 인터페이스를 상속(implements) 받아서` compareTo 메서드를 @Override 로 구현해야 한다.

```java
class AccSubItem implements Comparable<AccSubItem> {
    //getter, setter 생략
    @Override
    public int compareTo(AccSubItem other) {
        return other.getAccAmt().compareTo(this.getAccAmt()); // 내림차순으로 정렬
    }

    @Override
    public String toString() {
        return "AccSubItem{" +
                "accId='" + accId + '\'' +
                ", depWdrTypCd='" + depWdrTypCd + '\'' +
                ", accAmt=" + accAmt +
                '}';
    }
}
```