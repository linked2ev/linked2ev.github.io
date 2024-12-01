---
layout: post
title:  "[Practical-Java] 6. Stream"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바8부터 스트림이 도입되었으며, 이후 버전에서 기능이 확장되고 개선되었다. java.util.stream 패키지에 존재하며 스트림 객체는 원천 데이터가 변하지 않고 가공 된 데이터가 새로 생기는 개념의 `불변성` 특징이 있으며 그로인해 사이드 이팩트가 없으면서 병렬처리가 가능해진다.

<br>

# 1. 스트림(Stream)이란?

스트림은 데이터 처리 과정에서 코드를 더 간결하고 명확하게 만들어주는 장점을 갖고 있습니다. 또한, 병렬 처리를 통해 멀티코어 CPU를 활용하여 성능을 향상시킬 수 있는 기능도 제공한다.

```java
String[] strArr = { "eee", "fff", "aaa" };
List<String> strList = Arrays.asList(strArr);
```

```java
Stream<String> strStream1 = Arrays.stream(strArr);
Stream<String> strStream2 = strList.stream();
```

아래 데이터는 함수형 프로그래밍이 때문에 각 객체는 sort 된 상태로 출력은 되지만  
불변성으로 strStream1, strStream2는 원데이터는 변하지 않는다.

```java
strStream1.sorted().forEach(System.out::println);
strStream2.sorted().forEach(System.out::println);
```

<br>

## ◼︎ 스트림 특징

1. **`선언적 프로그래밍 방식 (Declarative Programming):`** 스트림은 선언적 프로그래밍을 지원합니다. 즉, 코드에서 데이터 처리 과정에 대한 로직을 명시하는 대신, 무엇을 원하는지에 중점을 둡니다. 이로써 가독성이 향상되고 유지보수가 쉬워집니다.

2. **`내부 반복 (Internal Iteration):`** 스트림은 내부 반복을 사용합니다. 내부 반복은 컬렉션 내의 요소를 반복하는 과정을 컬렉션 자체에서 처리하는 것을 의미합니다. 이는 개발자가 명시적으로 반복을 처리할 필요 없이 코드를 간결하게 만들어줍니다.

3. **`게으른 평가 (Lazy Evaluation):`** 스트림은 게으른 평가를 지원합니다. 게으른 평가란 요청 시에만 연산을 수행하는 것을 의미합니다. 이는 연산이 실제로 필요한 경우에만 수행되기 때문에 효율적으로 동작하게 합니다.

4. **`병렬 처리 지원 (Parallel Processing Support):`** 스트림은 병렬 처리를 지원합니다. 이는 멀티코어 프로세서를 활용하여 데이터를 병렬로 처리할 수 있습니다. `parallel()` 메서드를 호출하여 병렬 스트림을 생성할 수 있습니다.

5. **`불변성 (Immutable):`** 스트림 연산은 기존 데이터를 변경하지 않고 새로운 스트림을 반환합니다. 이는 함수형 프로그래밍의 불변성 개념을 따르며, 부작용(side effect)을 최소화합니다.

6. **`중간 및 최종 연산:`** 스트림 연산은 중간 연산과 최종 연산으로 나뉩니다. 중간 연산은 다른 스트림을 반환하고, 최종 연산은 최종 결과를 생성합니다. 이러한 연산 체인을 통해 다양한 데이터 처리 작업을 수행할 수 있습니다.

7. **`함수형 인터페이스 활용:`** 스트림 연산에는 주로 함수형 인터페이스가 사용됩니다. 람다 표현식을 활용하여 간결하고 유연한 코드를 작성할 수 있습니다.

스트림은 컬렉션, 배열 등 다양한 데이터 소스에서 사용할 수 있으며, 데이터를 필터링, 변환, 정렬, 그룹화 등 다양한 작업을 간결하게 수행할 수 있도록 도와준다.

<br><br>


## ◼︎ 지연된 연산(Lazy Evaluation)

`스트림 연산에서 최종 연산이 수행되기 전까지는 중간 연산이 수행되지 않는다. 최종 연산이 수행되어야 스트림의 요소들이 중간 연산을 거쳐 최종 연산에서 처리`된다.

`중간 연산자는 실제로 데이터를 처리하지 않고 새로운 스트림을 생성`한다. 스트림을 변환하거나 필터링하여 새로운 스트림을 반환하며 이들은 연속적으로 체이닝될 수 있고, 이 때 Lazy Evaluation의 특성을 갖는다. 중간 연산은 최종 연산이 호출될 때까지 실제로 처리되지 않으며, 중간 연산들의 체인은 연산을 연기한다.

`최종 연산자는 스트림의 요소를 소비하고 결과를 반환합니다. 이들은 스트림을 닫는 역할`을 하며, 최종 연산이 호출되어야 중간 연산자들이 실제로 동작합니다. 최종 연산이 호출되기 전까지는 중간 연산들이 실제로 실행되지 않는다.

<br><br>


# 2. 스트림의 중간연산

중간 연산자는 스트림을 반환하며, 연속적으로 연산을 수행할 수 있다. 이들은 '게으른(lazy)' 특성을 갖고 있어 최종 연산이 호출되기 전까지 실제 요소를 처리하지 않습니다. 주로 데이터를 변환하거나 필터링하고, 스트림의 요소를 수정하지 않고 새로운 스트림을 생성한다.

<br>


중간 연산 | 설명
----|----
Stream&lt;T&gt; filter(Predicate<? super T> predicate) | 주어진 조건에 맞는 요소만을 걸러냅니다.
&lt;R&gt; Stream&lt;R&gt; map(Function<? super T, ? extends R> mapper) | 요소들을 다른 형태로 변환합니다.
flatMap(Function<? super T, ? extends Stream<? extends R>> mapper) | 각 요소에 대해 매핑 함수를 적용하고, 이 함수는 각 요소를 다른 스트림으로 변환
Stream&lt;T&gt; distinct() | 중복을 제거합니다.
Stream&lt;T&gt; sorted() | 요소들을 정렬합니다.
Stream&lt;T&gt; sorted(Comparator<? super T> comparator) | 요소들을 정해진 순서대로 정렬합니다.
Stream&lt;T&gt; limit(long maxSize) | 요소 개수를 제한합니다.
Stream&lt;T&gt; skip(long n) | 처음 몇 개의 요소를 건너뜁니다.
Stream&lt;T&gt; peek(Consumer<? super T> action) | 각 요소를 가져와서 추가적인 작업을 수행하고 스트림을 반환합니다.

위의 각 중간 연산은 스트림을 변환하거나 필터링하거나 제한하는 등 다양한 작업을 수행합니다. 이러한 중간 연산자들을 조합하여 다양한 데이터 처리 작업을 할 수 있습니다.

<br><br>


## ◼︎ filter()

`stream.filter()` 메서드는 filter 내 조건이 true를 만족하는 요소들만을 선택하여 새로운 스트림을 반환한다. 

- Stream&lt;T&gt; filter(Predicate<? super T> predicate)
- 주어진 조건에 맞는 요소만을 걸러냅니다.
- [[Java] Stream Filter 연산](https://linked2ev.github.io/java/2023/11/28/Java-Stream-Filter-%EC%97%B0%EC%82%B0/)

```java
List<AccSubItem> filter1 = AccSubItems.stream().filter(accSubItem -> accSubItem.getAccAmt().compareTo(new BigDecimal(100000)) >= 0)
                                                .collect(Collectors.toList());
```

<br><br>


## ◼︎ map()

`stream.map()` 메서드는 스트림 내의 요소를 다른 형태로 변환하는 중요한 연산이다.

- &lt;R&gt; Stream&lt;R&gt; map(Function<? super T, ? extends R> mapper)
- 요소들을 다른 형태로 변환합니다.
- [[Java] Stream Map 연산](https://linked2ev.github.io/java/2023/11/28/Java-Stream-Map-%EC%97%B0%EC%82%B0/)

```java
AccSubItems.stream().map(AccSubItem::getAccId)
                    .map(e -> e.replaceAll("A", "B"))
                    .forEach(System.out::println);
```

<br><br>


## ◼︎ flatMap()

`stream.flatMap()`은 중첩된 구조의 요소들을 평면화(flatten)하는 데 사용된다. 

- flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)
- 각 요소에 대해 매핑 함수를 적용하고, 이 함수는 각 요소를 다른 스트림으로 변환
- [[Java] Stream FlatMap 연산](https://linked2ev.github.io/java/2023/11/29/Java-Stream-FlatMap-%EC%97%B0%EC%82%B0/)


```java
Stream<List<AccSubItem>> streamList = Stream.of(AccSubItems1, AccSubItems2);

//2개의 stream 을 flatMap() 으로 연산하는 경우
//아래와 같이 하나의 Stream 으로 병합되는 데이터 구조이다.
Stream<AccSubItem> retFlatMap = streamList.flatMap(List::stream);
retFlatMap.forEach(System.out::println);
```

<br><br>


## ◼︎ distinct()

`stream.distinct()` 메서드는 중복된 요소를 제거하여 고유한(unique) 요소들만으로 이루어진 스트림을 반환한다. 

- Stream&lt;T&gt; distinct()
-  중복을 제거합니다.
- [[Java] Stream Distinct 연산](https://linked2ev.github.io/java/2023/11/28/Java-Stream-Distinct-%EC%97%B0%EC%82%B0/)

```java
List<String> distinctTypeList = AccSubItems.stream().map(AccSubItem::getDepWdrTypCd)
                                                    .distinct()
                                                    .collect(Collectors.toList());
```

<br><br>


## ◼︎ sorted()

- Stream&lt;T&gt; sorted()
- Stream&lt;T&gt; sorted(Comparator<? super T> comparator)
- 요소들을 정렬합니다.
- [[Java] Stream Sort 연산](https://linked2ev.github.io/java/2023/11/28/Java-Stream-Sort-%EC%97%B0%EC%82%B0/)

```java
List<AccSubItem> defaultSortedList = AccSubItems.stream().sorted()
                                                            .collect(Collectors.toList());
```

<br><br>


## ◼︎ peek()

`stream.peek()`` 메서드는 중간 연산자로 스트림의 요소를 확인하거나 디버깅 목적으로 사용됩니다. peek() 메서드를 사용하여 스트림의 각 요소를 처리하고, 처리된 요소를 그대로 반환합니다. 

- Stream&lt;T&gt; peek(Consumer<? super T> action) 
- 각 요소를 가져와서 추가적인 작업을 수행하고 스트림을 반환합니다.
- [[Java] Stream Peek 연산](https://linked2ev.github.io/java/2023/11/28/Java-Stream-Peek-%EC%97%B0%EC%82%B0/)

```java
List<Integer> list = AccSubItems.stream()
                                .map(AccSubItem::getAccId)
                                .peek(e -> System.out.println("[STEP-1] AccId: " + e))
                                .filter(e -> e.contains("A"))
                                .peek(e -> System.out.println("  [STEP-2] A시작 Filter: " +e))
                                .map(e -> e.replaceAll("A", ""))
                                .map(Integer::parseInt)
                                .peek(e -> System.out.println("    [STEP-3] Replace 후 Integer 변환: " + e))
                                .filter(e -> e < 400)
                                .peek(e -> System.out.println("      [STEP-4] 400 이하 Filter: " + e))
                                .sorted()
                                .peek(e -> System.out.println("[ORDER] Sorted: " + e))
                                .collect(Collectors.toList());
```

<br><br>


# 3. 스트림의 최종연산

최종 연산자는 스트림 파이프라인의 끝을 나타내며, 스트림의 요소를 소모하여 실제 결과를 반환합니다. 이 연산자가 호출되기 전까지 중간 연산은 실제로 수행되지 않습니다. 최종 연산자는 스트림을 닫는 역할을 한다.


최종 연산 | 설명
---- | ----
void forEach(Consumer<? super T> action)<br>void forEachOrdered(Consumer<? super T> action) | 각 요소에 지정된 작업 수행
long count() | 스트림의 요소의 개수 반환
Optional&lt;T&gt; max(Comparator<? super T> comparator)<br>Optional&lt;T&gt; min(Comparator<? super T> comparator) | 스트림의 최대값/최소값 반환
Optional&lt;T&gt; findAny()<br>Optional&lt;T&gt; findFirst() | 스트림의 요소 아무거나 하나를 반환<br>스트림의 첫번째 요소 하나를 반환
boolean allMatch(Predicate&lt;T&gt; p)<br>boolean anyMatch(Predicate&lt;T&gt; p)<br>boolean noneMatch(Predicate&lt;T&gt; p) | 모든 요소를 만족하는지 확인<br>하나라도 요소를 만족하는지 확인<br>모든 요소를 만족시키지 않는지 확인
Object[] toArray()<br>A[] toArray(IntFunction&lt;A[]&gt; generator) | 스트림의 모든 요소를 배열로 반환
Optional&lt;T&gt; reduce(BinaryOperator&lt;T&gt; accumulator)<br>T reduce(&lt;T&gt; identity, BinaryOperator&lt;T&gt; accumulator)<br>U reduce(&lt;U&gt; identity, BiFunction&lt;U,T,U&gt; accumulator, BinaryOperator&lt;U&gt; combiner) | 스트림의 요소를 하나씩 줄여가면서(리듀싱) 계산
R collect(Collector&lt;T,A,R&gt; collector)<br>R collect(Supplier&lt;R&gt; supplier, BiConsumer&lt;R,T&gt; accumulator, BiConsumer&lt;R.R&gt; combiner) | 주로 요소를 그룹화하거나 분할한 결과를 컬렉션으로 반환

<br><br>


## ◼︎ forEach()

`stream.forEach()`은 중첩된 구조의 요소들을 평면화(flatten)하는 데 사용된다.

```java
//직렬로 모든 요소에 대해 지정 된 작업을 수행
void forEach(Consumer<? super T> action)   
//모든 요소에 대해 순서를 보장 된 작업을 수행, 병렬 스트림 경우 순서가 보장
void forEachOrdered(Consumer<? super T> action)
```

- [[Java] Stream ForEach 최종연산](https://linked2ev.github.io/java/2023/11/30/Java-Stream-ForEach-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0/)

```java
//기본 sequential() 직렬 스트림
IntStream.range(1, 10).sequential().forEach(System.out::print);
IntStream.range(1, 10).sequential().forEachOrdered(System.out::print);

//parallel() 병렬 스트림
//- forEach : 병렬 스트림 경우 순서가 보장되지 않음
IntStream.range(1, 10).parallel().forEach(System.out::print);
//- forEachOrdered : 병렬 스트림 경우 순서가 보장됨
IntStream.range(1, 10).parallel().forEachOrdered(System.out::print);
```

<br><br>


## ◼︎ count()

`stream.count()` 메서드는 해당 스트림에서 요소의 개수를 세는 데 사용된다.

- [[Java] Stream collect 최종연산과 Collector 인터페이스](https://linked2ev.github.io/java/2023/12/01/Java-Stream-collect-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0%EA%B3%BC-Collector-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/)

```java
//스트림의 전체 카운팅
long totCnt1 = accSubItems.stream().count();
long totCnt2 = accSubItems.stream().collect(Collectors.counting());
```

<br><br>


## ◼︎ min(), max()

`stream.min()/max()`는 스트림의 요소 중 가장 작은 값, 큰 값을 반환한다.

```java
- Optional<T> max(Comparator<? super T> comparator)
- Optional<T> min(Comparator<? super T> comparator)
```

- [[Java] Stream collect 최종연산과 Collector 인터페이스](https://linked2ev.github.io/java/2023/12/01/Java-Stream-collect-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0%EA%B3%BC-Collector-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/)


```java
Optional<Integer> max = numbers.stream().max(Integer::compareTo);
```

<br><br>


## ◼︎ match()

`stream에 조건 검사 allMath(), anyMatch(), noneMatch() 와 findFirst(), findAny()` 최종연산에 대한 포스팅이다.

```java
boolean allMatch(Predicate<? super T> predicate)      //모든 요소를 만족하는지 확인
boolean anyMatch(Predicate<? super T> predicate)      //하나라도 요소를 만족하는지 확인
boolean noneMatch(Predicate<? super T> predicate)     //모든 요소를 만족시키지 않는지 확인
```

- [[Java] Stream Match, Find 최종연산](https://linked2ev.github.io/java/2023/11/30/Java-Stream-Match,-Find-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0/)

```java
boolean allPositive = numbers.stream().allMatch(num -> num > 0);
System.out.println("모든 요소가 양수인가요? " + allPositive);

boolean anyNegative = numbers.stream().anyMatch(num -> num < 0);
System.out.println("하나라도 음수인 요소가 있나요? " + anyNegative);

boolean noneZero = numbers.stream().noneMatch(num -> num == 0);
System.out.println("모든 요소가 0이 아닌가요? " + noneZero);
```

<br><br>


## ◼︎ find()

두 메서드 모두 `Optional`을 반환하는데, 이는 스트림에서 요소를 찾지 못했을 경우에 대해서이다. `findFirst()`와 `findAny()`는 요소를 찾으면 해당 요소를 담은 `Optional`을 반환하고, 요소가 없는 경우에는 `Optional.empty()`를 반환한다.

```java
Optional<T> findFirst()         //첫번째 요소를 반환, 순차 스트림에 사용
Optional<T> findAny()           //아무 요소 하나를 반환, 병렬 스트림에 사용
```

- [[Java] Stream Match, Find 최종연산](https://linked2ev.github.io/java/2023/11/30/Java-Stream-Match,-Find-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0/)

```java
Optional<AccSubItem> retFirst = AccSubItems.stream()
                                        .filter(e -> e.getAccId().startsWith("B"))
                                        .findFirst();
System.out.println("# findFirst = " + retFirst.get());   //순차적으로 filter 조건에 맞는 "B222"만 반환

Optional<AccSubItem> retAny = AccSubItems.parallelStream()
                                        .filter(e -> e.getAccId().startsWith("B"))
                                        .findAny();
System.out.println("# retAny = " + retAny.get());        //병렬처리시에 filter 조건에 맞는 아무 쓰레드에서 반환
```

<br><br>


## ◼︎ reduce()

`stream.reduce()`는 스트림의 모든 요소를 하나로 줄이는 연산을 수행합니다.

```java
Optional<T> reduce(BinaryOperator<T> accumulator)
T reduce(T identity, BinaryOperator<T> accumulator)
U reduce(U identity, BiFunction<U,T,U> accumulator, BinaryOperator<U> combiner)
```

- identity : 초기값
- accumulator : 이전 연산결과와 스트림의 요소에 수행할 연산
- combiner : 병렬처리된 결과를 합치는데 사용할 연산(병렬 스트림) 
- [[Java] Stream Reduce 최종연산](https://linked2ev.github.io/java/2023/11/30/Java-Stream-Reduce-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0/)

```java
//T reduce(T identity, BinaryOperator<T> accumulator)
int count = numbers.stream().reduce(0, (a, b) -> a+1);
System.out.println("# count : " + count);

int sum = numbers.stream().reduce(0, (a, b) -> a+b);
System.out.println("# sum : " + sum);

int min = numbers.stream().reduce(Integer.MAX_VALUE, (a, b) -> a<b ? a:b);
System.out.println("# min : " + min);

int max = numbers.stream().reduce(Integer.MIN_VALUE, (a, b) -> a>b ? a:b);
System.out.println("# max : " + max);

//Optional<T> reduce(BinaryOperator<T> accumulator)
Optional<Integer> optMin = numbers.stream().reduce(Integer::min);
System.out.println("# optMin : " + optMin.get());

Optional<Integer> optMax = numbers.stream().reduce(Integer::max);
System.out.println("# optMax : " + optMax.get());
```

<br><br>


# 1-1. `스트림의 컬렉션 변환`

## ◼︎ collect()

`Collector 인터페이스는 스트림의 collect() 메서드`와 함께 사용되며, 스트림의 요소를 여러 그룹으로 분할하거나, 요소들을 리스트, 세트, 맵 등의 컬렉션으로 수집하고, 통계 수치를 계산하는 등의 기능을 수행합니다.

- [[Java] Stream collect 최종연산과 Collector 인터페이스](https://linked2ev.github.io/java/2023/12/01/Java-Stream-collect-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0%EA%B3%BC-Collector-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/)

<br>

> ◼︎ toList()

```java
// Collectors.toList()를 사용하여 List로 수집
List<AccSubItem2> collectedToList = accSubItems.stream()
        .collect(Collectors.toList());
```

<br>

> ◼︎ toSet()

```java
// Collectors.toSet()를 사용하여 Set으로 수집
Set<AccSubItem2> collectedToSet = accSubItems.stream()
        .collect(Collectors.toSet());
```

<br>

> ◼︎ toMap()

```java
// Collectors.toMap()를 사용하여 Map으로 수집 (AccId 를 key로 사용)
// Key duplication 오류로 toMap() 사용 시 mergeFunction 지정
// -> (existing, replacement) -> existing 부분은 중복 키가 발생했을 때 기존 값(existing)을 유지하는 방법
Map<String, AccSubItem2> collectedToMap = accSubItems.stream()
        .collect(Collectors.toMap(AccSubItem2::getAccId, item -> item, (existing, replacement) -> existing));
```

<br>

> ◼︎ toArray()

```java
// toArray 를 활용해 List 를 Array 형태로 변환
AccSubItem2[] collectedToArray = accSubItems.stream().toArray(AccSubItem2[]::new);
```

<br><br>


# 1-2. `스트림의 통계`

- [[Java] Stream collect 최종연산과 Collector 인터페이스](https://linked2ev.github.io/java/2023/12/01/Java-Stream-collect-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0%EA%B3%BC-Collector-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/)

<br>

> ◼︎ counting() : 전체 건수 구하기

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

> ◼︎ summingInt() : 합계 구하기

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

> ◼︎ reducing() : reduce 로 BigDecimal 합계 구하기

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

> ◼︎ min(), max() : 최소, 최대 값 구하기

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

> ◼︎ min(), max() 에 Comparator.comparingInt() 활용

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

> ◼︎ minBy(), maxBy() 에 Comparator.comparing() 활용

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

> ◼︎ averagingInt() : 평균 구하기

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


# 1-3. `스트림 문자열 결합`

- [[Java] Stream collect 최종연산과 Collector 인터페이스](https://linked2ev.github.io/java/2023/12/01/Java-Stream-collect-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0%EA%B3%BC-Collector-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/)

<br>

> ◼︎ joining()

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


# 1-4. `스트림 GroupingBy 그룹 연산`

`Collectors.groupingBy()` 는 스트림 요소를 지정된 기준에 따라 그룹화하는 기능으로 이를 통해 데이터를 그룹별로 분류하고 집계할 수 있다. 

```java
Collector groupingBy(Function classifier)
Collector groupingBy(Function classifier, Collector downstream)
Collector groupingBy(Function classifier, Supplier mapFactory, Collector downstream)
```

- [[Java] Stream collect 최종연산과 Collector 인터페이스](https://linked2ev.github.io/java/2023/12/01/Java-Stream-collect-%EC%B5%9C%EC%A2%85%EC%97%B0%EC%82%B0%EA%B3%BC-Collector-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/)

<br>


> ◼︎ groupingBy 기본 예제

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


> ◼︎ groupingBy n분할 일반적인 그룹화

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


> ◼︎ groupingBy n분할 정렬/역정렬 그룹화

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


> ◼︎ groupingBy n분할 통계

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

> ◼︎ groupingBy 다중 분할(DepWdrTypCd, Score)

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

<br><br>


# 1-5. `스트림 PartitioningBy 분할 연산`

`Collectors.partitioningBy()`은 요소들을 참(true)인 그룹과 거짓(false)인 boolean 그룹으로 분할해서 두 개의 그룹을 맵(Map)으로 반환한다. partitioningBy 는 `2분할로 Key가 boolean 으로 true, false` 이다.

```java
Collector partitioningBy(Predicate predicate)
Collector partitioningBy(Predicate predicate, Collector downStream)
```

<br>


> ◼︎ partitioningBy 분할

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


> ◼︎ partitioningBy 통계

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


> ◼︎ partitioningBy 분할 + 통계(min)

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

> ◼︎ partitioningBy 분할 + 통계(max)

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

> ◼︎ partitioningBy 다중 분할

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

<br><br>
