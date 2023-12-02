---
layout: post
title:  "[Java] Stream GroupingBy 그룹 연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`Collectors.groupingBy()` 는 스트림 요소를 지정된 기준에 따라 그룹화하는 기능으로 이를 통해 데이터를 그룹별로 분류하고 집계할 수 있다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>

# Stream Collectors.groupingBy 그룹

```java
Collector groupingBy(Function classifier)
Collector groupingBy(Function classifier, Collector downstream)
Collector groupingBy(Function classifier, Supplier mapFactory, Collector downstream)
```

<br>

```java
List<AccSubItem2> accSubItems = Arrays.asList(
        new AccSubItem2("A123", "10", new BigDecimal(11000), 700),
        new AccSubItem2("A123", "10", new BigDecimal(4000), 120),
        new AccSubItem2("A123", "20", new BigDecimal(103000), 670),
        new AccSubItem2("A201", "10", new BigDecimal(128000), 320),
        new AccSubItem2("A222", "20", new BigDecimal(100000), 900),
        new AccSubItem2("A510", "10", new BigDecimal(21000), 820),
        new AccSubItem2("A510", "10", new BigDecimal(37000), 790),
        new AccSubItem2("A510", "10", new BigDecimal(432000), 240),
        new AccSubItem2("A610", "20", new BigDecimal(431000), 510),
        new AccSubItem2("A105", "10", new BigDecimal(12346), 880)
);
```

<br>

> groupingBy 기본 예제

```java
// DepWdrTypCd 멤버변수 기준으로 groupBy 별 건수
Map<String, Long> accSubGroupByCnt = accSubItems.stream()
        .collect(Collectors.groupingBy(AccSubItem2::getDepWdrTypCd, Collectors.counting()));
System.out.println("DepWdrTypCd 멤버변수 기준으로 groupBy 별 건수: " + accSubGroupByCnt);

// DepWdrTypCd 멤버변수 기준으로 groupBy 별로 객체 List
Map<String, List<AccSubItem2>> accSubGroupByMap = accSubItems.stream()
                                                        .collect(Collectors.groupingBy(AccSubItem2::getDepWdrTypCd));
accSubGroupByMap.get("10").forEach(accSubItem -> {
    System.out.println("[10]" + accSubItem.toString());
});
accSubGroupByMap.get("20").forEach(accSubItem -> {
    System.out.println("[20]" + accSubItem.toString());
});
```

```
DepWdrTypCd 멤버변수 기준으로 groupBy 별 건수: {20=3, 10=7}
[10]AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=11000, score=700}
[10]AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=4000, score=120}
[10]AccSubItem2{accId='A201', depWdrTypCd='10', accAmt=128000, score=320}
[10]AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=21000, score=820}
[10]AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=37000, score=790}
[10]AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}
[10]AccSubItem2{accId='A105', depWdrTypCd='10', accAmt=12346, score=880}
[20]AccSubItem2{accId='A123', depWdrTypCd='20', accAmt=103000, score=670}
[20]AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=100000, score=900}
[20]AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}
```

<br>


> groupingBy n분할 일반적인 그룹화

그룹화 된 그룹명이 정렬 되지 않은 상태이다.

```java
// n분할 그룹화
Map<String, List<AccSubItem2>> gradeGroupBy = accSubItems.stream()
        .collect(Collectors.groupingBy(
                accSubItem -> {
                    if (accSubItem.getScore() >= 900) return "A 등급";
                    else if (accSubItem.getScore() >= 800) return "B 등급";
                    else if (accSubItem.getScore() >= 700) return "C 등급";
                    else return "D 등급";
                }
        ));

System.out.println("========== 일반적인 n분할 그룹화 ==========");
gradeGroupBy.forEach((key, accSubItem) -> {
    System.out.println("[" + key + "]");
    accSubItem.forEach(System.out::println);
});
```

```
========== 일반적인 n분할 그룹화 ==========
[C 등급]
AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=11000, score=700}
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=37000, score=790}
[D 등급]
AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=4000, score=120}
AccSubItem2{accId='A123', depWdrTypCd='20', accAmt=103000, score=670}
AccSubItem2{accId='A201', depWdrTypCd='10', accAmt=128000, score=320}
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}
AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}
[B 등급]
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=21000, score=820}
AccSubItem2{accId='A105', depWdrTypCd='10', accAmt=12346, score=880}
[A 등급]
AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=100000, score=900}
```

<br>


> groupingBy n분할 정렬/역정렬 그룹화

TreeMap 을 활용한 정렬/역정렬 grouping 예제이다.

```java
TreeMap<String, List<AccSubItem2>> orderGradeGroupBy = accSubItems.stream()
        .collect(Collectors.groupingBy(
                accSubItem -> {
                    if (accSubItem.getScore() >= 900) return "A 등급";
                    else if (accSubItem.getScore() >= 800) return "B 등급";
                    else if (accSubItem.getScore() >= 700) return "C 등급";
                    else return "D 등급";
                }
                , TreeMap::new  //A등급, B등급, C등급, D등급
                , Collectors.toList()
            )
        );

System.out.println("========== 정렬 n분할 그룹화 ==========");
orderGradeGroupBy.forEach((key, accSubItem) -> {
    System.out.println("[정렬][" + key + "]");
    accSubItem.forEach(System.out::println);
});

System.out.println("========== 정렬 역순 n분할 그룹화 ==========");
orderGradeGroupBy.descendingMap().forEach((key, accSubItem) -> {
    System.out.println("[정렬 역순][" + key + "]");
    accSubItem.forEach(System.out::println);
});
```

```
========== 정렬 n분할 그룹화 ==========
[정렬][A 등급]
AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=100000, score=900}
[정렬][B 등급]
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=21000, score=820}
AccSubItem2{accId='A105', depWdrTypCd='10', accAmt=12346, score=880}
[정렬][C 등급]
AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=11000, score=700}
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=37000, score=790}
[정렬][D 등급]
AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=4000, score=120}
AccSubItem2{accId='A123', depWdrTypCd='20', accAmt=103000, score=670}
AccSubItem2{accId='A201', depWdrTypCd='10', accAmt=128000, score=320}
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}
AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}
========== 정렬 역순 n분할 그룹화 ==========
[정렬 역순][D 등급]
AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=4000, score=120}
AccSubItem2{accId='A123', depWdrTypCd='20', accAmt=103000, score=670}
AccSubItem2{accId='A201', depWdrTypCd='10', accAmt=128000, score=320}
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}
AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}
[정렬 역순][C 등급]
AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=11000, score=700}
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=37000, score=790}
[정렬 역순][B 등급]
AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=21000, score=820}
AccSubItem2{accId='A105', depWdrTypCd='10', accAmt=12346, score=880}
[정렬 역순][A 등급]
AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=100000, score=900}
```

<br>


> groupingBy n분할 통계

```java
TreeMap<String, Long> orderGradeGroupByCnt = accSubItems.stream()
        .collect(Collectors.groupingBy(
                accSubItem -> {
                    if (accSubItem.getScore() >= 900) return "A 등급";
                    else if (accSubItem.getScore() >= 800) return "B 등급";
                    else if (accSubItem.getScore() >= 700) return "C 등급";
                    else return "D 등급";
                }
                , TreeMap::new  //A등급, B등급, C등급, D등급
                , Collectors.counting()
            )
        );
System.out.println("========== 정렬 n분할 그룹화 통계 ==========");
orderGradeGroupByCnt.forEach((key, groupCnt) -> {
    System.out.println("[" + key + "] cnt : "  + groupCnt);
});
```

```
========== 정렬 n분할 그룹화 통계 ==========
[A 등급] cnt : 1
[B 등급] cnt : 2
[C 등급] cnt : 2
[D 등급] cnt : 5
```

<br>

> groupingBy 다중 분할(DepWdrTypCd, Score)

```java
TreeMap<String, Map<String, List<AccSubItem2>>> multiGroupBy = accSubItems.stream()
        .collect(Collectors.groupingBy(
                AccSubItem2::getDepWdrTypCd, TreeMap::new, Collectors.groupingBy(
                        accSubItem -> {
                            if (accSubItem.getScore() >= 900) return "A 등급";
                            else if (accSubItem.getScore() >= 800) return "B 등급";
                            else if (accSubItem.getScore() >= 700) return "C 등급";
                            else return "D 등급";
                        }, TreeMap::new, Collectors.toList()
                )
        ));

System.out.println("========== 다중 분할(DepWdrTypCd, Score) 그룹화 ==========");
multiGroupBy.forEach((depWdrKey, scoreGroup) -> {
    System.out.println("# DepWdrTypCd: " + depWdrKey);
    scoreGroup.forEach((scoreKey, items) -> {
        System.out.println("\tScore: " + scoreKey);
        items.forEach(e-> System.out.println("        - " + e));
    });
});
```

```
========== 다중 분할(DepWdrTypCd, Score) 그룹화 ==========
# DepWdrTypCd: 10
	Score: B 등급
        - AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=21000, score=820}
        - AccSubItem2{accId='A105', depWdrTypCd='10', accAmt=12346, score=880}
	Score: C 등급
        - AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=11000, score=700}
        - AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=37000, score=790}
	Score: D 등급
        - AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=4000, score=120}
        - AccSubItem2{accId='A201', depWdrTypCd='10', accAmt=128000, score=320}
        - AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}
# DepWdrTypCd: 20
	Score: A 등급
        - AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=100000, score=900}
	Score: D 등급
        - AccSubItem2{accId='A123', depWdrTypCd='20', accAmt=103000, score=670}
        - AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}
```