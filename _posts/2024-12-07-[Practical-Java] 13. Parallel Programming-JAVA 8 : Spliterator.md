---
layout: post
title:  "[Practical-Java] 13. Parallel Programming-JAVA 8 : Spliterator"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---


자바의 `Spliterator`는 Java 8에서 도입된 인터페이스로, `Iterator`와 `Iterable` 인터페이스의 확장된 것이다. `Spliterator`는 "splitable iterator"의 줄임말로, 요소를 순차적으로 탐색하면서 필요에 따라 분할할 수 있는 기능을 제공한다. 이는 주로 병렬 프로그래밍을 위해 설계되었다. `Collection` 인터페이스를 구현하는 클래스는 `spliterator()` 메서드를 통해 `Spliterator` 객체를 제공할 수 있다.

<br>


# 병렬 프로그래밍 : JAVA 8-분할반복

# 1. 개념 및 정의

`Spliterator`는 컬렉션의 요소를 탐색하고, 필요한 경우 분할할 수 있는 방법을 제공하는 인터페이스다. 분할 기능은 병렬 처리를 용이하게 하기 위해 설계되었으며, `Spliterator`는 `tryAdvance`, `trySplit`, `estimateSize`, `characteristics`와 같은 주요 메서드를 포함한다.

### 특징

1. **분할 가능성(TrySplit)**: `Spliterator`의 가장 중요한 특징은 `trySplit()` 메서드를 통해 자신을 부분적으로 분할하여 일부 요소를 포함하는 새로운 `Spliterator`를 반환할 수 있다는 점이다. 이는 병렬 처리에서 작업을 여러 스레드에 분배할 때 유용하다.
2. **정확한 크기 추정(EstimateSize)**: `estimateSize()` 메서드는 탐색해야 할 요소의 대략적인 수를 반환한다. 이는 분할 전략을 결정하는 데 도움을 준다.
3. **특성(Characteristics)**: `characteristics()` 메서드는 `Spliterator`의 특성을 나타내는 정수를 반환한다. 이 특성들은 `Spliterator`가 정렬된 순서(SORTED), 고정된 크기(SIZED), 요소의 변경 불가능(IMMUTABLE), 그리고 더 많은 특성을 포함할 수 있다.

### 장점

1. **향상된 병렬 처리**: `Spliterator`는 병렬 스트림 처리에 적합하도록 설계되어 있으며, 병렬 처리를 통해 프로그램의 성능을 향상시킬 수 있다.
2. **효율적인 요소 처리**: 각 `Spliterator`는 독립적으로 요소를 처리할 수 있으며, 이는 데이터 구조를 효율적으로 탐색하고 처리할 수 있게 한다.
3. **유연한 처리 전략**: 다양한 특성과 분할 가능성을 통해, 개발자는 데이터를 처리하는 데 필요한 전략을 유연하게 선택할 수 있다.

`Spliterator`는 특히 큰 데이터 셋을 다룰 때나 멀티 코어 프로세서의 이점을 최대한 활용하고자 할 때 매우 유용하다. 예를 들어, Java의 `Stream` API에서는 `Spliterator`를 사용하여 요소들을 병렬로 처리하고 결과를 효율적으로 합치는 작업을 수행한다. 이러한 처리는 데이터의 분산 처리 및 빠른 접근이 필요할 때 큰 이점을 제공한다.

Java의 `Spliterator` 인터페이스는 여러 주요 메서드를 제공한다. 이들 각각의 메서드는 컬렉션 또는 스트림의 요소를 탐색하고, 분할하는데 특화된 기능을 수행한다. 여기에 `Spliterator`의 주요 메서드들을 설명하겠다:

<br>


# 2. 주요 메서드

### 1. `tryAdvance(Consumer<? super T> action)`
- **기능**: `Spliterator`의 요소들을 순차적으로 소비하며, 각 요소에 대해 지정된 `action`을 수행한다. `action`은 `Consumer` 인터페이스를 구현한 람다 표현식 또는 메서드 참조일 수 있다.
- **반환 값**: 요소가 있어 `action`을 수행했다면 `true`를, 더 이상 처리할 요소가 없으면 `false`를 반환한다.

### 2. `trySplit()`
- **기능**: `Spliterator`를 두 부분으로 분할하려고 시도한다. 호출 결과로, 원래 `Spliterator`의 일부 요소를 포함하는 새로운 `Spliterator` 객체가 반환된다.
- **반환 값**: 성공적으로 분할된 경우 새로운 `Spliterator`를 반환하고, 분할할 수 없는 경우 `null`을 반환한다. 이 메서드는 주로 병렬 처리에 사용된다.

### 3. `estimateSize()`
- **기능**: 탐색해야 할 요소들의 추정 개수를 반환한다. 이 값은 정확하지 않을 수 있으며, 단지 추정치다.
- **반환 값**: 남아 있는 요소들의 추정 개수를 `long` 타입으로 반환한다.

### 4. `characteristics()`
- **기능**: `Spliterator`의 특성을 나타내는 비트 값 집합을 반환한다. 이 특성들은 `Spliterator`의 행동 방식을 정의하며, 예를 들어 정렬된 순서(SORTED), 요소 크기가 고정(SIZED), 변경 불가능(IMMUTABLE) 등을 포함할 수 있다.
- **반환 값**: 특성을 나타내는 `int` 비트 값이다.

### 5. `getComparator()`
- `characteristics()` 메서드에 `SORTED` 플래그가 설정된 경우, 이 메서드를 사용하여 `Spliterator`가 정렬된 순서를 유지하는 데 사용하는 `Comparator` 객체를 얻을 수 있다.
- **반환 값**: `Spliterator`가 정렬된 경우, 해당 `Comparator`를 반환한다. 정렬되지 않은 경우 `IllegalStateException`을 발생시킨다.

### 6. `forEachRemaining(Consumer<? super T> action)`
- **기능**: 남아 있는 모든 요소에 대해 주어진 `action`을 수행한다. 이 메서드는 내부적으로 `tryAdvance`를 반복적으로 호출하여 모든 요소를 처리한다.
- **반환 값**: 없다 (`void`). 모든 요소에 대해 `action`을 실행한다.

<br>

# 3. Spliterator 이용한 병렬 처리

Spliterator 는 분할하는 역할을 하지 병렬 처리를 하려면 Fork/Join 프레임워크와 결합해서 구현해야 한다.

```java
try {
    List<PayAccount> payAccountList = getDummyBankAccountList();

    // Spliterator 객체 생성
    Spliterator<PayAccount> spliterator1 = payAccountList.spliterator();
    // Spliterator 분리
    Spliterator<PayAccount> spliterator2 = spliterator1.trySplit();

    // 첫 번째 부분 처리
    Runnable task1 = ()-> {
        spliterator1.forEachRemaining((spliterator)->{
            try {
                Thread.sleep(1000);
                System.out.println("# ID: " + spliterator.getAccountId() + ", ThreadName: " + Thread.currentThread().getName());
            } catch (InterruptedException ex) {
                ex.printStackTrace();
            }
        });
    };

    // 두 번째 부분 처리
    Runnable task2 = ()-> {
        spliterator2.forEachRemaining((spliterator)->{
            try {
                Thread.sleep(1000);
                System.out.println("# ID: " + spliterator.getAccountId() + ", ThreadName: " + Thread.currentThread().getName());
            } catch (InterruptedException ex) {
                ex.printStackTrace();
            }
        });
    };

    // 스레드 생성 및 시작
    Thread thread1 = new Thread(task1);
    Thread thread2 = new Thread(task2);
    thread1.start();
    thread2.start();

    // 스레드가 끝날 때까지 대기
    thread1.join();
    thread2.join();

} catch (InterruptedException e) {
    e.printStackTrace();
}
```

<br><br>


---