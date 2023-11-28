---
layout: post
title:  "[Java] Stream GroupBy 연산 기록"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`Collectors.groupingBy`` 는 스트림 요소를 지정된 기준에 따라 그룹화하는 기능으로 이를 통해 데이터를 그룹별로 분류하고 집계할 수 있다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 기록(기억).

<br><br>

# Stream Collectors.groupingBy 예제

```java
public class ExStreamGroupBy {

    public static void main(String[] args) {
        List<AccSubItem> AccSubItems = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("A222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("A610", "20", new BigDecimal(431000)),
                new AccSubItem("A105", "10", new BigDecimal(12346))
        );

        // DepWdrTypCd 멤버변수 기준으로 groupBy 별 건수
        Map<String, Long> accSubGroupByCnt = AccSubItems.stream()
                .collect(Collectors.groupingBy(AccSubItem::getDepWdrTypCd, Collectors.counting()));
        System.out.println("DepWdrTypCd 멤버변수 기준으로 groupBy 별 건수: " + accSubGroupByCnt);

        // DepWdrTypCd 멤버변수 기준으로 groupBy 별로 객체 List
        Map<String, List<AccSubItem>> accSubGroupByMap = AccSubItems.stream()
                                                                .collect(Collectors.groupingBy(AccSubItem::getDepWdrTypCd));
        accSubGroupByMap.get("10").forEach(accSubItem -> {
            System.out.println("[10]" + accSubItem.toString());
        });
        accSubGroupByMap.get("20").forEach(accSubItem -> {
            System.out.println("[20]" + accSubItem.toString());
        });
    }
}
```

```
DepWdrTypCd 멤버변수 기준으로 groupBy 별 건수: {20=2, 10=4}
[10]AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
[10]AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
[10]AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
[10]AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
[20]AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
[20]AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
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