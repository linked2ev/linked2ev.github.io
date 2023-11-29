---
layout: post
title:  "[Java] Stream Distinct 연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.distinct()` 메서드는 중복된 요소를 제거하여 고유한(unique) 요소들만으로 이루어진 스트림을 반환한다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream Distinct 예제

List 객체에서는 map 을 이용해서 고유한 요소로만 list 형태에서 distinct() 중복제거 후 새로운 list 생성해야 한다.

```java
public class ExStreamDistinct {

    public static void main(String[] args) {
        List<AccSubItem> AccSubItems = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("A222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("A610", "20", new BigDecimal(431000)),
                new AccSubItem("A105", "10", new BigDecimal(12346))
        );

        // DepWdrTypCd 기준으로 map 을 이용해서 고유한 요소로만 list 형태에서 distinct() 중복제거 후 새로운 list 생성
        List<String> distinctTypeList = AccSubItems.stream().map(AccSubItem::getDepWdrTypCd)
                                                            .distinct()
                                                            .collect(Collectors.toList());
        System.out.println("distinctTypeList: " +  distinctTypeList);
    }
}
```

```
distinctTypeList: [10, 20]
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