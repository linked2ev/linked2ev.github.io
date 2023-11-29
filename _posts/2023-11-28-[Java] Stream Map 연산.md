---
layout: post
title:  "[Java] Stream Map 연산"
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

        System.out.println("1. AccId 멤버변수만 map 을 이용해 변환 후에 replaceAll");
        //1. AccId 멤버변수만 map 을 이용해 변환 후에 replaceAll
        AccSubItems.stream().map(AccSubItem::getAccId)
                            .map(e -> e.replaceAll("A", "B"))
                            .forEach(System.out::println);


        System.out.println("--------------------------------------------------");
        System.out.println("2. 객체 내 특정 값 AccId 만 map 을 이용해 변환 replaceAll");
        //2. 객체 내 특정 값 AccId 만 map 을 이용해 변환 replaceAll
        AccSubItems.stream().map(accSubItem -> {
            if ("20".equals(accSubItem.getDepWdrTypCd())) {
                accSubItem.setAccId(accSubItem.getAccId().replaceAll("A", "B"));
            }
            return accSubItem;
        }).forEach(System.out::println);


        System.out.println("--------------------------------------------------");
        System.out.println("3. accAmt 요소만 map 을 이용해 변환 후에 합산");
        // 3. accAmt 요소만 map 을 이용해 변환 후에 합산
        BigDecimal totalAccAmt = AccSubItems.stream()
                .map(AccSubItem::getAccAmt)
                .reduce(BigDecimal.ZERO, BigDecimal::add);
        System.out.println("accAmt 합계: " + totalAccAmt);
    }
}
```

```
1. AccId 멤버변수만 map 을 이용해 변환 후에 replaceAll
B123
B201
B222
B510
B610
B105
--------------------------------------------------
2. 객체 내 특정 값 AccId 만 map 을 이용해 변환 replaceAll
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='B222', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='B610', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
--------------------------------------------------
3. accAmt 요소만 map 을 이용해 변환 후에 합산
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