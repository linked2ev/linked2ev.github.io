---
layout: post
title:  "[Java] Stream Map 연산 기록"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.map()` 메서드는 스트림 내의 요소를 다른 형태로 변환하는 중요한 연산 중 하나로 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>

# Stream map 예제

```java
public class ExStreamMap {

    public static void main(String[] args) {
        List<AccSubItem> AccSubItems = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("A222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("A610", "20", new BigDecimal(431000)),
                new AccSubItem("A105", "10", new BigDecimal(12346))
        );

        // AccId 멤버변수만 map 을 이용해서 새로운 list 생성
        List<String> accIdList = AccSubItems.stream().map(AccSubItem::getAccId)
                                                     .collect(Collectors.toList());
        System.out.println("AccId List: " + accIdList);

        // accAmt 합산
        BigDecimal totalAccAmt = AccSubItems.stream().map(AccSubItem::getAccAmt)
                                                     .reduce(BigDecimal.ZERO, BigDecimal::add);
        System.out.println("accAmt 합계: " + totalAccAmt);
    }
}
```

```
AccId List: [A123, A201, A222, A510, A610, A105]
accAmt 합계: 691346
```

<br>

> AccSubItem 클래스 일부

```java
class AccSubItem {   
    //getter, setter 생략
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