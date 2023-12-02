---
layout: post
title:  "[Java] Stream PartitioningBy 분할 연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`Collectors.partitioningBy()`은 요소들을 참(true)인 그룹과 거짓(false)인 boolean 그룹으로 분할해서 두 개의 그룹을 맵(Map)으로 반환한다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream Collectors.partitioningBy 분할

```java
Collector partitioningBy(Predicate predicate)
Collector partitioningBy(Predicate predicate, Collector downStream)
```

partitioningBy 는 `2분할로 Key가 boolean 으로 true, false` 이다.

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


> partitioningBy 분할

```java
//DepWdrTypCd "10"과 "20"으로 partitioningBy 분할
Map<Boolean, List<AccSubItem2>> partitioningBy = accSubItems.stream()
        .collect(Collectors.partitioningBy(item -> "10".equals(item.getDepWdrTypCd())));
System.out.println("# partitioningBy");
System.out.println("- DepWdrTypCd(10) : " + partitioningBy.get(true));
System.out.println("- DepWdrTypCd(20) : " + partitioningBy.get(false));
```

```
# partitioningBy
- DepWdrTypCd(10) : [AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=11000, score=700}, AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=4000, score=120}, AccSubItem2{accId='A201', depWdrTypCd='10', accAmt=128000, score=320}, AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=21000, score=820}, AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=37000, score=790}, AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}, AccSubItem2{accId='A105', depWdrTypCd='10', accAmt=12346, score=880}]
- DepWdrTypCd(20) : [AccSubItem2{accId='A123', depWdrTypCd='20', accAmt=103000, score=670}, AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=100000, score=900}, AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}]
```

<br>


> partitioningBy 통계

```java
//DepWdrTypCd "10"과 "20"으로 partitioningBy 통계
Map<Boolean, Long> partitioningByCnt = accSubItems.stream()
        .collect(Collectors.partitioningBy(item -> "10".equals(item.getDepWdrTypCd()), Collectors.counting()));
System.out.println("# partitioningByCnt");
System.out.println("- DepWdrTypCd(10) Cnt : " + partitioningByCnt.get(true));
System.out.println("- DepWdrTypCd(20) Cnt : " + partitioningByCnt.get(false));
```

```
# partitioningByCnt
- DepWdrTypCd(10) Cnt : 7
- DepWdrTypCd(20) Cnt : 3
```

<br>


> partitioningBy 분할 + 통계(min)

```java
//DepWdrTypCd "10"과 "20"으로 partitioningBy 통계 Integer 형 Min Max
Map<Boolean, Optional<AccSubItem2>> partitioningByMin = accSubItems.stream()
        .collect(Collectors.partitioningBy(
                item -> "10".equals(item.getDepWdrTypCd())
                , Collectors.minBy(Comparator.comparingInt(AccSubItem2::getScore))
        ));
System.out.println("# partitioningBy Integer Min");
System.out.println("- DepWdrTypCd(10) By Score Min : " + partitioningByMin.get(true));
System.out.println("- DepWdrTypCd(20) By Score Min : " + partitioningByMin.get(false));
```

```
# partitioningBy Integer Min
- DepWdrTypCd(10) By Score Min : Optional[AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=4000, score=120}]
- DepWdrTypCd(20) By Score Min : Optional[AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}]
```

<br>

> partitioningBy 분할 + 통계(max)

```java
//DepWdrTypCd "10"과 "20"으로 partitioningBy 통계 BigDecimal 형 Min Max
Map<Boolean, Optional<AccSubItem2>> partitioningByMax = accSubItems.stream()
        .collect(Collectors.partitioningBy(
                item -> "10".equals(item.getDepWdrTypCd()),
                Collectors.maxBy(Comparator.comparing(AccSubItem2::getAccAmt))
        ));
System.out.println("# partitioningBy BigDecimal Max");
System.out.println("- DepWdrTypCd(10) By AccAmt Max : " + partitioningByMax.get(true));
System.out.println("- DepWdrTypCd(20) By AccAmt Max : " + partitioningByMax.get(false));
```

```
# partitioningBy BigDecimal Max
- DepWdrTypCd(10) By AccAmt Max : Optional[AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}]
- DepWdrTypCd(20) By AccAmt Max : Optional[AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}]
```

<br>

> partitioningBy 다중 분할

```java
//DepWdrTypCd "10"과 "20" 와 AccAmt 100000 이상 partitioningBy 다중 분할
Map<Boolean, Map<Boolean, List<AccSubItem2>>> partitionInPartitioningBy = accSubItems.stream()
        .collect(
                Collectors.partitioningBy(item -> "10".equals(item.getDepWdrTypCd())
                , Collectors.partitioningBy(item -> item.getAccAmt().compareTo(new BigDecimal(100000)) >= 0)
        ));
System.out.println("# partition In PartitioningBy ");
System.out.println("- DepWdrTypCd(10) By 100000 이상 By : " + partitionInPartitioningBy.get(true).get(true));
System.out.println("- DepWdrTypCd(20) By 100000 이상 By : " + partitionInPartitioningBy.get(false).get(true));
```

```
# partition In PartitioningBy 
- DepWdrTypCd(10) By 100000 이상 By : [AccSubItem2{accId='A201', depWdrTypCd='10', accAmt=128000, score=320}, AccSubItem2{accId='A510', depWdrTypCd='10', accAmt=432000, score=240}]
- DepWdrTypCd(20) By 100000 이상 By : [AccSubItem2{accId='A123', depWdrTypCd='20', accAmt=103000, score=670}, AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=100000, score=900}, AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}]
```
