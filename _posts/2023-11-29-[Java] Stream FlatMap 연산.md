---
layout: post
title:  "[Java] Stream FlatMap 연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.flatMap()`은 중첩된 구조의 요소들을 평면화(flatten)하는 데 사용된다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream FlatMap 예제

```java
public class ExStreamFlatMap {

    public static void main(String[] args) {

        List<AccSubItem> AccSubItems1 = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("A222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("B610", "20", new BigDecimal(431000)),
                new AccSubItem("B105", "10", new BigDecimal(12346))
        );
        List<AccSubItem> AccSubItems2 = Arrays.asList(
                new AccSubItem("B223", "10", new BigDecimal(100000)),
                new AccSubItem("B121", "10", new BigDecimal(120000)),
                new AccSubItem("C689", "20", new BigDecimal(1000)),
                new AccSubItem("C987", "10", new BigDecimal(27000)),
                new AccSubItem("C141", "20", new BigDecimal(431000)),
                new AccSubItem("C287", "10", new BigDecimal(12346))
        );

        System.out.println("====================== Map() 사용시 ======================");
        Stream<List<AccSubItem>> streamList = Stream.of(AccSubItems1, AccSubItems2);
        //2개의 stream 을 map() 으로 연산하는 경우
        //아래와 같이 Stream 안에 Stream 데이터 구조이다.
        Stream<Stream<AccSubItem>> retMap = streamList.map(List::stream);
        retMap.forEach(subItemStream -> {
            System.out.println("# Stream 안에 Stream");
            subItemStream.forEach(System.out::println);
        });


        System.out.println("");
        System.out.println("====================== FlatMap() 사용시 ======================");
        streamList = Stream.of(AccSubItems1, AccSubItems2);

        //2개의 stream 을 flatMap() 으로 연산하는 경우
        //아래와 같이 하나의 Stream 으로 병합되는 데이터 구조이다.
        Stream<AccSubItem> retFlatMap = streamList.flatMap(List::stream);
        retFlatMap.forEach(System.out::println);
    }
}
```

```
====================== Map() 사용시 ======================
# Stream 안에 Stream
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='B610', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='B105', depWdrTypCd='10', accAmt=12346}
# Stream 안에 Stream
AccSubItem{accId='B223', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='B121', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='C689', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='C987', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='C141', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='C287', depWdrTypCd='10', accAmt=12346}

====================== FlatMap() 사용시 ======================
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='B610', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='B105', depWdrTypCd='10', accAmt=12346}
AccSubItem{accId='B223', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='B121', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='C689', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='C987', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='C141', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='C287', depWdrTypCd='10', accAmt=12346}
```