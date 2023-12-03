---
layout: post
title:  "[Java] Stream collect 최종연산과 Collector 인터페이스"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

Stream `collect() 메서드`와 `Collector 인터페이스`에 대한 포스팅이다. stream의 Collector는 스트림의 요소를 어떤 형태로든 수집할 때 사용되는 인터페이스입니다. 이 인터페이스는 스트림에서 요소를 수집하고 그 결과를 취합하는 방법을 정의합니다. 기존 시스템이 레거시라 자바에서 데이터를 처리해야 할 상황이 와서 뇌에 기록(기억).

<br><br>


# Stream `collect() 메서드`와 `Collector 인터페이스`

Collector 인터페이스는 스트림의 collect() 메서드와 함께 사용되며, 스트림의 요소를 여러 그룹으로 분할하거나, 요소들을 리스트, 세트, 맵 등의 컬렉션으로 수집하고, 통계 수치를 계산하는 등의 기능을 수행합니다.

<br>

> Collect() 는 Collector 를 매개변수로 하는 스트림의 최종연산

```java
Object collect(Collector collector) //Collector 를 구현한 클래스의 객체를 매개변수로
```

> `Collector`는 수집(collect)에 필요한 메서드를 정의해 놓은 `인터페이스`

```java
//T(요소)를 A에 누적한 다음, 결과를 R로 변환해서 반환
public interface Collector<T,A,R> {
    // ...
}
```

> `Collectors 클래스`는 다양한 기능의 컬렉터(Collector를 구현한 클래스)를 제공

- 변환 : mapping(), toList(), toMap(), toCollection(), ...
- 통계 : counting(), summingInt(), averagingInt(), maxBy(), minBy(), summarizingInt(), ...
- 문자열 결합 : joining()
- 리듀싱 : reducing()
- 그룹화와 분활 : groupingBy(), partitioningBy(), collectingAndThen()

<br>


# 1. `스트림의 컬렉션 변환` 

- toList(), toSet(), toMap(), ...

```java
List<AccSubItem2> accSubItems = Arrays.asList(
        new AccSubItem2("A123", "10", new BigDecimal(100000), 700),
        new AccSubItem2("A123", "10", new BigDecimal(100000), 120),
        new AccSubItem2("A123", "10", new BigDecimal(100000), 670),
        new AccSubItem2("A201", "10", new BigDecimal(120000), 320),
        new AccSubItem2("A222", "20", new BigDecimal(1000), 900),
        new AccSubItem2("A510", "10", new BigDecimal(27000), 820),
        new AccSubItem2("A510", "10", new BigDecimal(27000), 790),
        new AccSubItem2("A510", "10", new BigDecimal(27000), 240),
        new AccSubItem2("A610", "20", new BigDecimal(431000), 510),
        new AccSubItem2("A105", "10", new BigDecimal(12346), 880)
);
```

<br>

## ◼︎ toList()

```java
// Collectors.toList()를 사용하여 List로 수집
List<AccSubItem2> collectedToList = accSubItems.stream()
        .collect(Collectors.toList());

System.out.println("toList() 결과: ");
collectedToList.forEach(System.out::println);
```

```
toList() 결과: 
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
```

<br>

## ◼︎ toSet()


```java
// Collectors.toSet()를 사용하여 Set으로 수집
Set<AccSubItem2> collectedToSet = accSubItems.stream()
        .collect(Collectors.toSet());

System.out.println("toSet() 결과: ");
collectedToSet.forEach(System.out::println);
```

```
toSet() 결과: 
AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
```

<br>

## ◼︎ toMap()

```java
// Collectors.toMap()를 사용하여 Map으로 수집 (AccId 를 key로 사용)
// Key duplication 오류로 toMap() 사용 시 mergeFunction 지정
// -> (existing, replacement) -> existing 부분은 중복 키가 발생했을 때 기존 값(existing)을 유지하는 방법
Map<String, AccSubItem2> collectedToMap = accSubItems.stream()
        .collect(Collectors.toMap(AccSubItem2::getAccId, item -> item, (existing, replacement) -> existing));

System.out.println("toMap() 결과: ");
collectedToMap.forEach((key, value) -> System.out.println(key + ": " + value));
```

```
toMap() 결과: 
A610: AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
A105: AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
A201: AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
A510: AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
A222: AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
A123: AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
```

<br><br>


# 2. `스트림의 배열`

- toArray()

## ◼︎ toArray()

```java
// toArray 를 활용해 List 를 Array 형태로 변환
AccSubItem2[] collectedToArray = accSubItems.stream().toArray(AccSubItem2[]::new);

for (int i=0; i<collectedToArray.length; i++){
    System.out.println( i + ": " + collectedToArray[i]);
};
```

```
0: AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
1: AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
2: AccSubItem{accId='A123', depWdrTypCd='10', accAmt=100000}
3: AccSubItem{accId='A201', depWdrTypCd='10', accAmt=120000}
4: AccSubItem{accId='A222', depWdrTypCd='20', accAmt=1000}
5: AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
6: AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
7: AccSubItem{accId='A510', depWdrTypCd='10', accAmt=27000}
8: AccSubItem{accId='A610', depWdrTypCd='20', accAmt=431000}
9: AccSubItem{accId='A105', depWdrTypCd='10', accAmt=12346}
```

<br><br>


# 3. `스트림의 통계`

- counting(), summmingInt(), maxBy(), minBy(), averagingInt(), ...

## ◼︎ counting() : 전체 건수 구하기

```java
//스트림의 전체 카운팅
long totCnt1 = accSubItems.stream().count();
long totCnt2 = accSubItems.stream().collect(Collectors.counting());
System.out.println("# 1 .totCnt1 = " + totCnt1 + ",  totCnt2 = " + totCnt2);
```

```
# 1 .totCnt1 = 10,  totCnt2 = 10
```

<br>

## ◼︎ summingInt() : 합계 구하기

```java
//int 형 전체 합계 점수
int sumScore1 = accSubItems.stream().mapToInt(AccSubItem2::getScore).sum();
int sumScore2 = accSubItems.stream().collect(Collectors.summingInt(AccSubItem2::getScore));
System.out.println("# 2. sumScore1 = " + sumScore1 + ",  sumScore2 = " + sumScore2);
```

```
# 2. sumScore1 = 5950,  sumScore2 = 5950
```

<br>

## ◼︎ reducing() : reduce 로 BigDecimal 합계 구하기

```java
//BigDecimal 형 데이터 전체 합계 금액은 reduce() 로 구하기 예제
BigDecimal sumAmt1 = accSubItems.stream()
                                .map(AccSubItem2::getAccAmt)
                                .reduce(BigDecimal.ZERO, BigDecimal::add);
BigDecimal sumAmt2 = accSubItems.stream()
                                .map(AccSubItem2::getAccAmt)
                                .collect(Collectors.reducing(BigDecimal.ZERO, BigDecimal::add));
System.out.println("# 3. sumAmt1 = " + sumAmt1);
System.out.println("#    sumAmt2 = " + sumAmt2);
```

```
# 3. sumAmt1 = 945346
#    sumAmt2 = 945346
```

<br>

## ◼︎ min(), max() : 최소, 최대 값 구하기

```java
//mapToInt(Integer) , mapToLong(Long) 타입 min, max 구하기
OptionalInt minScore = accSubItems.stream()
                                .mapToInt(AccSubItem2::getScore)
                                .min();
OptionalInt maxScore = accSubItems.stream()
                                .mapToInt(AccSubItem2::getScore)
                                .max();
System.out.println("# 4. minScore = " + minScore.getAsInt() + ",  maxScore = " + maxScore.getAsInt());
```

```
# 4. minScore = 120,  maxScore = 900
```

<br>

## ◼︎ min(), max() 에 Comparator.comparingInt() 활용

```java
//AccSubItem 객체 중에 score 점수기준으로 min, max 객체 구하기
Optional<AccSubItem2> minAccSubItem = accSubItems.stream()
                                                .min(Comparator.comparingInt(AccSubItem2::getScore));
Optional<AccSubItem2> maxAccSubItem = accSubItems.stream()
                                                .max(Comparator.comparingInt(AccSubItem2::getScore));
System.out.println("# 5. minAccSubItem = " + minAccSubItem);
System.out.println("     maxAccSubItem = " + maxAccSubItem);
```

```
# 5. minAccSubItem = Optional[AccSubItem2{accId='A123', depWdrTypCd='10', accAmt=100000, score=120}]
     maxAccSubItem = Optional[AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=1000, score=900}]
```

<br>

## ◼︎ minBy(), maxBy() 에 Comparator.comparing() 활용

```java
//accAmt 변수 그룹 기준으로 min, max 객체 구하기
Optional<AccSubItem2> minByDepWdrTypCd = accSubItems.stream()
                                                    .collect(Collectors.minBy(Comparator.comparing(AccSubItem2::getAccAmt)));
Optional<AccSubItem2> maxByDepWdrTypCd = accSubItems.stream()
                                                    .collect(Collectors.maxBy(Comparator.comparing(AccSubItem2::getAccAmt)));
System.out.println("# 6. minByDepWdrTypCd = " + minByDepWdrTypCd);
System.out.println("     maxByDepWdrTypCd = " + maxByDepWdrTypCd);
```

```
# 6. minByDepWdrTypCd = Optional[AccSubItem2{accId='A222', depWdrTypCd='20', accAmt=1000, score=900}]
     maxByDepWdrTypCd = Optional[AccSubItem2{accId='A610', depWdrTypCd='20', accAmt=431000, score=510}]
```

<br>

## ◼︎ averagingInt() : 평균 구하기

```java
// 7. score를 기준으로 평균 계산하여 출력
Double averageScore = accSubItems.stream()
                                .collect(Collectors.averagingInt(AccSubItem2::getScore));
System.out.println("# 7. Score 의 평균 = " + averageScore);
```

```
# 7. Score 의 평균 = 595.0
```

<br><br>


# 4. `스트림 문자열 결합` 

- joining() 

## ◼︎ joining()

```java
String joiningStr = accSubItems.stream().map(AccSubItem2::getAccId).collect(Collectors.joining());
System.out.println("# joiningStr1 = " + joiningStr);

joiningStr = accSubItems.stream().map(AccSubItem2::getAccId).collect(Collectors.joining("|"));
System.out.println("# joiningStr2 = " + joiningStr);

joiningStr =accSubItems.stream().map(AccSubItem2::getAccId).collect(Collectors.joining(",", "{", "}"));
System.out.println("# joiningStr3 = " + joiningStr);
```

```
# joiningStr1 = A123A123A123A201A222A510A510A510A610A105
# joiningStr2 = A123|A123|A123|A201|A222|A510|A510|A510|A610|A105
# joiningStr3 = {A123,A123,A123,A201,A222,A510,A510,A510,A610,A105}
```

<br><br>


# 5. 예제 코드 AccSubItem2 클래스 일부 

```java

class AccSubItem2 implements Comparable<AccSubItem2> {
    
    //getter, setter 생략

    /**
     * AccSubItem2 클래스에서 equals()와 hashCode() 메서드를 오버라이딩해서 구현하면 toSet()을 사용하여 중복된 객체를 제거할 수 있다.
     * 중요한 점은 equals()와 hashCode()가 객체의 식별에 사용되는 필드들을 올바르게 비교하고 해시코드를 생성
     */
    @Override
    public int compareTo(AccSubItem2 other) {
        return other.getAccAmt().compareTo(this.getAccAmt()); // 내림차순으로 정렬
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        AccSubItem2 that = (AccSubItem2) o;
        return Objects.equals(accId, that.accId) &&
                Objects.equals(depWdrTypCd, that.depWdrTypCd) &&
                Objects.equals(accAmt, that.accAmt);
    }

    @Override
    public int hashCode() {
        return Objects.hash(accId, depWdrTypCd, accAmt);
    }

    @Override
    public String toString() {
        return "AccSubItem2{" +
                "accId='" + accId + '\'' +
                ", depWdrTypCd='" + depWdrTypCd + '\'' +
                ", accAmt=" + accAmt +
                ", score=" + score +
                '}';
    }
}
```

<br><br>