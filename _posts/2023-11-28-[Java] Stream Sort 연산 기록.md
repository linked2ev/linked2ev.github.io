---
layout: post
title:  "[Java] Stream Sort 연산 기록"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.sorted()` 메서드는 요소들을 정렬하는 데 사용된다. 이 메서드는 요소들의 기본 정렬 순서를 사용하거나, `Comparator`를 제공하여 사용자 지정 정렬 순서를 지정할 수 있다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>

# Stream Sort 예제

```java
public class ExStreamSortComp {

    public static void main(String[] args) {
        List<AccSubItem> AccSubItems = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("A222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("A610", "20", new BigDecimal(431000)),
                new AccSubItem("A105", "10", new BigDecimal(12346))
        );

        System.out.println("---------- 1. AccSubItem 클래스에 @override 된 compareTo 로 sort ----------");

        // AccSubItem 클래스에 @override 된 compareTo 로 sort
        List<AccSubItem> defaultSortedList = AccSubItems.stream().sorted()
                                                                 .collect(Collectors.toList());
        defaultSortedList.stream().forEach(accSubItem -> {
            System.out.println(accSubItem.toString());
        });

        System.out.println("---------- 2. AccSubItem 클래스에 @override 된 compareTo 의 reverse sort ----------");

        // AccSubItem 클래스에 @override 된 compareTo 로 sort
        List<AccSubItem> reverseSortedList = AccSubItems.stream().sorted(Comparator.reverseOrder())
                                                                 .collect(Collectors.toList());
        reverseSortedList.stream().forEach(accSubItem -> {
            System.out.println(accSubItem.toString());
        });

        System.out.println("---------- 3. AccAmt 멤버변수로 오름차순 sort ----------");

        // AccAmt 멤버변수로 오름차순 sort
        List<AccSubItem> amtSortedList = AccSubItems.stream().sorted(Comparator.comparing(AccSubItem::getAccAmt))
                                                             .collect(Collectors.toList());
        amtSortedList.stream().forEach(accSubItem -> {
            System.out.println(accSubItem.toString());
        });

        System.out.println("---------- 4. DepWdrTypCd, AccId 멤버변수 순으로 오름차순 sort ----------");

        // DepWdrTypCd, AccId 멤버변수 순으로 오름차순 sort
        List<AccSubItem> typThenAccIdSortedList = AccSubItems.stream().sorted(Comparator.comparing(AccSubItem::getDepWdrTypCd)
                                                                                        .thenComparing(AccSubItem::getAccId))
                                                                      .collect(Collectors.toList());
        typThenAccIdSortedList.stream().forEach(accSubItem -> {
            System.out.println(accSubItem.toString());
        });
    }
}
```

```
---------- 1. AccSubItem 클래스에 @override 된 compareTo 로 sort ----------
AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
---------- 2. AccSubItem 클래스에 @override 된 compareTo 의 reverse sort ----------
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
---------- 3. AccAmt 멤버변수로 오름차순 sort ----------
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
---------- 4. DepWdrTypCd, AccId 멤버변수 순으로 오름차순 sort ----------
AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
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