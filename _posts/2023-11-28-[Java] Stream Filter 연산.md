---
layout: post
title:  "[Java] Stream Filter 연산"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

`stream.filter()` 메서드는 filter 내 조건이 true를 만족하는 요소들만을 선택하여 새로운 스트림을 반환한다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream Filter 예제

```java
public class ExStreamFilter {

    public static void main(String[] args) {
        List<AccSubItem> AccSubItems = Arrays.asList(
                new AccSubItem("A123", "10", new BigDecimal(100000)),
                new AccSubItem("A201", "10", new BigDecimal(120000)),
                new AccSubItem("A222", "20", new BigDecimal(1000)),
                new AccSubItem("A510", "10", new BigDecimal(27000)),
                new AccSubItem("A610", "20", new BigDecimal(431000)),
                new AccSubItem("A105", "10", new BigDecimal(12346))
        );


        //1. 일반적인 filter
        List<AccSubItem> filter1 = AccSubItems.stream().filter(accSubItem -> accSubItem.getAccAmt().compareTo(new BigDecimal(100000)) >= 0)
                                                       .collect(Collectors.toList());
        System.out.println("filter1: " +  filter1);

        //2. Predicate 를 이용한 필터링
        //Predicate 를 사용하여 동일한 조건으로 구현할 수 있습니다. Predicate 는 특정 조건을 표현하는 함수형 인터페이스입니다.
        List<AccSubItem> filter2 = AccSubItems.stream().filter(accSubItem -> isAmountMoreThan100000().test(accSubItem))
                                      .collect(Collectors.toList());
        System.out.println("filter2: " +  filter2);

        //3. filter 2개 조건, DepWdrTypCd 이면서 100000 원 이상
        Predicate<AccSubItem> isEquals20 = item -> "20".equals(item.getDepWdrTypCd());

        List<AccSubItem> filter3 = AccSubItems.stream().filter(accSubItem -> isAmountMoreThan100000().test(accSubItem))
                                                       .filter(isEquals20)
                                                       .collect(Collectors.toList());
        System.out.println("filter3: " +  filter3);

        //4. filter3 과 동일한 조건
        List<AccSubItem> filter4 = AccSubItems.stream().filter(accSubItem -> {
                                                            if (accSubItem.getAccAmt().compareTo(new BigDecimal("100000")) >= 0
                                                                && "20".equals(accSubItem.getDepWdrTypCd())
                                                            ) {
                                                                return true;
                                                            }
                                                            return false;
                                                        }).collect(Collectors.toList());
        System.out.println("filter4: " +  filter4);
    }

    // Predicate 정의 (100000 원 이상)
    static Predicate<AccSubItem> isAmountMoreThan100000() {
        return item -> item.getAccAmt().compareTo(new BigDecimal("100000")) >= 0;
    }
}
```

```
filter1: [AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}, AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}, AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}]
filter2: [AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}, AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}, AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}]
filter3: [AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}]
filter4: [AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}]
```

<br>

> AccSubItem 클래스 일부

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