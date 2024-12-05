---
layout: post
title:  "[Practical-Java] 9. Parallel Programming-JAVA 7 : Fork, Join"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바 5에서 컨커런트API가 나오고 자바 7에서 `Fork(포크)/Join(조인)` 프레임워크가 나왔다. Fork/Join 는 java.uitl.concurrent 패키지의 ExecutorService 인터페이스를 구현한 클래스이다.

<br>


# 병렬 프로그래밍 : 자바 7-Fork(포크)/Join(조인)

컨커런트API에서의 Fork/Join 프레임워크는 병렬 컴퓨팅을 위한 자바의 한 도구로, 주로 멀티 코어 프로세서의 이점을 최대한 활용하기 위해 설계되었다. 이 프레임워크는 대규모 작업을 작은 작업으로 나누어서 병렬로 처리한 다음, 결과를 합치는 방식으로 작동한다. Fork는 이 작업의 분할을 의미하고, Join은 결과의 병합을 의미한다. 자바에서는 이를 ForkJoinPool 클래스를 통해 구현한다.

- Fork : 다른 프로세스 혹은 스레드(태스크)를 여러 개로 쪼개서 새롭게 생성한다는 의미
- Join : 포크해서 실행한 프로세스 혹은 스레드(태스크)의 결과를 취합한다는 의미

<br>

# 1. Fork/Join 개념

### Fork(포크)

"Fork" 단계에서는 큰 문제를 해결하기 위해 작은 문제로 분할한다.

- **병렬 처리**: 분할된 작은 작업들은 별도의 스레드에서 동시에 수행될 수 있어 전체 작업의 처리 시간을 줄일 수 있다.
- **작업의 균등 분배**: 작업을 여러 부분으로 나누어 각 스레드가 동일한 양의 작업을 할 수 있도록 함으로써 효율적인 리소스 활용이 가능하다.

### Join(조인)

"Join" 단계에서는 Fork 단계에서 분할된 작업들이 완료되면, 이들의 결과를 병합해 최종 결과를 도출한다.

- **결과 통합**: 각 하위 작업의 결과를 합쳐서 전체 작업의 최종 결과를 생성한다.
- **동기화**: 모든 하위 작업이 완료될 때까지 최종 결과를 도출할 수 없으므로, 작업의 완료를 기다리는 동기화 과정이 필요하다.

<br><br>


# 2. Fork/Join의 실행 환경

자바에서는 이런 패러다임을 `ForkJoinPool` 클래스를 통해 구현합니다. 이는`Work-Stealing Algorithm(작업 도둑 알고리즘)`을 사용하여 비효율적인 작업 분배와 리소스 낭비를 최소한다. 스레드가 실행할 작업이 없을 때 다른 스레드의 작업 큐에서 작업을 "도둑질"하여 가져와 처리하는 개념이다.

<br>

> **`Work-Stealing Algorithm(작업 도둑 알고리즘)`** : <br>Fork/Join 프레임워크의 핵심은 작업 도둑 알고리즘에 있습니다. 이 알고리즘을 통해, 스레드는 자신의 작업 큐가 비어 있을 때 다른 스레드의 큐에서 대기 중인 작업을 가져와 처리합니다. 이 방식은 스레드 간의 작업 부하를 균등하게 분배하여 리소스를 효율적으로 활용할 수 있게 돕습니다.

<br>

## 2-1. ForkJoinPool

ForkJoinPool은 자바7에 도입 된 Java의 java.util.concurrent 패키지에 속하는 클래스로 고급 병렬 프로그래밍을 위한 풀이다. ForkJoinTask의 인스턴스들(RecursiveAction 또는 RecursiveTask)를 사용하여 작업을 분할하고 결과를 병합합니다.

<br>

### 주요 메서드

- **`invoke(ForkJoinTask<T> task)`**:
   - 지정된 태스크를 실행하고 결과를 반환된다. 메서드 호출이 블로킹되므로 태스크가 완료될 때까지 기다린다.
   - 이 메서드는 주로 메인 스레드에서 복잡한 재귀 태스크를 처리할 때 사용된다.

- **`execute(ForkJoinTask<?> task)`**:
   - 태스크를 비동기로 실행하고 결과를 반환하지 않는다.
   - 이 메서드는 반환 값이 중요하지 않을 때 사용된다.

- **`submit(ForkJoinTask<T> task)`**:
   - 태스크를 비동기로 실행하고 `Future<T>`를 반환하여 나중에 결과를 조회할 수 있다.
   - `execute`와 비슷하지만, `Future`를 통해 작업의 결과나 상태를 나중에 확인할 수 있다는 점에서 차이가 있다.

- **`shutdown()`**:
   - 풀의 모든 스레드를 정상적으로 종료시키는 명령을 실행한다. 이미 제출된 작업은 완료되지만, 새로운 작업은 수락하지 않는다.

- **`shutdownNow()`**:
   - 풀의 작업을 즉시 중단하고 가능한 한 빨리 풀을 종료한다. 실행 중인 작업은 중단될 수 있으며, 반환된 목록에는 완료되지 않은 작업들이 포함될 수 있다.

- **`awaitTermination(long timeout, TimeUnit unit)`**:
   - `shutdown()` 호출 후, 지정된 시간 동안 현재 스레드가 블록되면서 풀의 모든 태스크가 종료될 때까지 기다린다.


```java
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class ExampleTask extends RecursiveTask<Integer> {
    @Override
    protected Integer compute() {
        // 재귀 작업 정의
        return 100;
    }

    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool(); // 풀 생성
        ExampleTask task = new ExampleTask(); // 태스크 생성

        Integer result = pool.invoke(task); // 태스크 실행 및 결과 기다림
        System.out.println("Result: " + result);

        pool.shutdown(); // 풀 종료
    }
}
```

<br>


## 2-2. ForkJoinTask

`ForkJoinTask`는 Fork/Join 프레임워크에서 실행할 수 있는 java.util.concurrent 패키지에 있는 추상 클래스이다. `ForkJoinPool`에서 실행될 작업을 정의한다. 직접 인스턴스를 생성할 수 없으며, `RecursiveTask`과 `RecursiveAction`에서 상속 받아서 병렬 작업을 정의하고 실행한다. ForkJoinTask는 작업을 분할하고 결과를 합치는 데 필요한 주요 메커니즘을 제공하며, `작업을 재귀적으로 분할하여 각 하위 작업을 병렬로 실행할 수 있도록 설계`되어 있다.

<br>

### Fork/Join 처리 과정

- **`fork()`**:
    - 현재 작업을 ForkJoinPool의 작업 큐에 비동기로 제출한다. 다른 스레드에서 이 작업을 수행할 수 있도록 스케줄링한다.
    - **처리 과정**: 큰 작업을 더 작은 작업으로 나누는 과정입니다. compute() 메서드 내에서 작업의 크기가 특정 임계값보다 큰 경우, 작업은 두 개 이상의 작은 작업으로 나누어집니다. 이 분할된 작업은 fork() 메서드를 호출하여 비동기적으로 실행할 수 있습니다.

- **`join()`**:
    - 작업이 완료될 때까지 현재 스레드를 블로킹하고, 작업의 결과를 반환한다. fork() 메서드에 의해 시작된 작업의 결과를 기다릴 때 사용된다.
    - **처리 과정**: 분할된 작업들이 완료되면, 그 결과는 join() 메서드를 호출하여 합쳐집니다. join()은 해당 작업이 완료될 때까지 현재 스레드를 대기시키는 역할을 한다. 이렇게 하여 분할된 작업들의 결과를 모아 최종 결과를 도출한다.


### 하위 클래스

- **`RecursiveAction`**: 결과를 반환하지 않는 작업을 위한 클래스이다. `compute()` 메서드를 구현하여 작업 로직을 정의한다.
- **`RecursiveTask`**: 결과를 반환하는 작업을 위한 클래스이다. 이 클래스는 `compute()` 메서드에서 계산 결과를 반환하는 방식으로 구현된다.

<br>


## 2-3. RecursiveTask 와 RecursiveAction

## RecursiveTask

`RecursiveTask`는 Java의 `java.util.concurrent` 패키지에 속하는 클래스로, Fork/Join 프레임워크에서 사용되며 결과를 반환하는 작업을 정의할 때 사용된다. `RecursiveTask`는 `ForkJoinTask`의 추상 하위 클래스로서, 복잡한 작업을 더 작은, 재귀적으로 처리 가능한 부분으로 나눈 다음, 각 부분의 결과를 결합하여 최종 결과를 반환한다.

#### 1. **결과 반환**
`RecursiveTask`는 처리 결과를 반환하는 작업을 정의할 때 사용된다. 이는 주로 수치 계산, 대규모 데이터 분석, 복잡한 알고리즘 등의 작업에서 유용하다. `RecursiveTask`의 인스턴스는 `compute()` 메서드를 구현하여 작업을 수행하고 결과를 반환한다.

#### 2. **재귀적 작업 분할**
작업의 크기가 임계값보다 크면, `RecursiveTask`를 재귀적으로 분할하여 작은 단위로 나눕니다. 각각의 분할된 작업은 동일한 타입의 새로운 `RecursiveTask` 인스턴스로 생성되어 처리된다.

#### 3. **병렬 처리**
분할된 작업은 `ForkJoinPool`을 통해 병렬로 처리된다. 작업을 시작할 때 `fork()` 메서드를 사용하여 작업을 비동기적으로 실행할 수 있으며, `join()` 메서드를 사용하여 분할된 작업의 결과를 기다리고 받아온다.

### compute() 메서드
`compute()` 메서드는 `RecursiveTask`의 핵심으로, 작업의 실제 로직을 구현하는 곳이다. `compute()` 메서드는 임계값을 기준으로 작업을 직접 수행할지, 아니면 더 작은 작업으로 분할할지를 결정한다.


```java
package com.modern.java.practical.concurrentapi.forkjoin;

import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

/**
 * RecursiveTask 를 사용하여 정수 배열의 최대값을 병렬로 찾는 MaxTask 클래스를 구현
 * 배열을 분할하고 각 부분의 최대값을 병렬로 계산한 후, 결과를 병합하여 전체 배열의 최대값을 도출
 */
public class FindMaxTaskExample extends RecursiveTask<Integer> {
    private final int[] array;
    private final int start;
    private final int end;
    private static final int THRESHOLD = 1000; // 배열 분할 임계값

    public FindMaxTaskExample(int[] array, int start, int end) {
        this.array = array;
        this.start = start;
        this.end = end;
    }

    //재귀적 분할 compute 메서드는 배열의 길이를 비교하여
    //왼쪽 태스크는 fork() 메서드를 통해 비동기적으로 실행되고, 오른쪽 태스크는 현재 스레드에서 compute()를 호출하여 직접 처리
    @Override
    protected Integer compute() {
        int length = end - start;
        if (length <= THRESHOLD) {
            // 작은 단위의 작업은 직접 계산
            return findMax(array, start, end);
        } else {
            // 큰 단위의 작업은 더 작게 분할
            int mid = start + length / 2;
            FindMaxTaskExample leftTask = new FindMaxTaskExample(array, start, mid);
            FindMaxTaskExample rightTask = new FindMaxTaskExample(array, mid, end);

            leftTask.fork(); // 왼쪽 작업 분할 실행
            int rightResult = rightTask.compute(); // 오른쪽 작업 실행
            int leftResult = leftTask.join(); // 왼쪽 작업 결과 기다림

            return Math.max(leftResult, rightResult); // 결과 병합
        }
    }

    private int findMax(int[] array, int start, int end) {
        int max = array[start];
        for (int i = start + 1; i < end; i++) {
            if (array[i] > max) {
                max = array[i];
            }
        }
        return max;
    }

    public static void main(String[] args) {
        int[] array = { 1, 5, 3, 8, 12, 7, 9, 10, 20, 11, 4, 17, 2, 6 };
        ForkJoinPool pool = new ForkJoinPool();
        FindMaxTaskExample task = new FindMaxTaskExample(array, 0, array.length);

        //invoke 메서드는 태스크가 완료될 때까지 블로킹하고, 태스크의 결과로 최대값을 반환
        int max = pool.invoke(task);

        System.out.println("# 최대값 : " + max);
        pool.shutdown();
    }
}
```

<br>

## RecursiveAction

`RecursiveAction`는 Java의 `java.util.concurrent` 패키지의 일부로, `Fork/Join` 프레임워크 내에서 사용되며, 결과를 반환하지 않는 작업을 정의할 때 사용된다. `RecursiveTask`와 비슷하지만, `RecursiveAction`은 결과를 반환하지 않는 주요 차이점이 있다. 이 클래스는 주로 사이드 이펙트(side effect)를 목적으로 하는 작업(예: 데이터 정렬, 파일 시스템 작업 등)에 사용된다.


#### 1. **결과 반환하지 않음**
`RecursiveAction`은 작업의 완료 후 결과를 반환하지 않는다. 이는 `compute()` 메서드에서 `void`를 반환한다는 의미이다.

#### 2. **재귀적 작업 분할**
작업의 크기가 임계값보다 클 경우, `RecursiveAction`은 재귀적으로 분할되어 더 작은 단위의 작업으로 나뉘어진다. 각 분할된 작업은 `RecursiveAction`의 새 인스턴스로 생성되어 병렬로 처리된다.

#### 3. **병렬 처리**
분할된 작업은 `ForkJoinPool`을 통해 병렬로 처리됩니다. 작업을 시작할 때 `fork()` 메서드를 사용해 비동기적으로 실행되고, `join()` 메서드로 분할된 작업들의 완료를 기다린다.

### compute() 메서드
`compute()` 메서드는 `RecursiveAction`의 핵심이며, 작업의 실제 실행 로직을 포함한다. 이 메서드는 작업을 직접 수행하거나, 임계값을 기준으로 더 작은 작업으로 분할하는 결정을 한다.


```java
package com.modern.java.practical.concurrentapi.forkjoin;

import java.util.Arrays;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveAction;

/**
 * 배열을 분할하고, 각 부분을 병렬로 정렬한 후, 병합합니다.
 */
public class SortTaskExample extends RecursiveAction {
    private final int[] array;
    private final int start;
    private final int end;
    private static final int THRESHOLD = 1000; // 임계값 설정

    public SortTaskExample(int[] array, int start, int end) {
        this.array = array;
        this.start = start;
        this.end = end;
    }

    @Override
    protected void compute() {
        int length = end - start;
        if (length <= THRESHOLD) {
            Arrays.sort(array, start, end); // 배열 직접 정렬
        } else {
            int mid = start + length / 2;
            SortTaskExample leftTask = new SortTaskExample(array, start, mid);
            SortTaskExample rightTask = new SortTaskExample(array, mid, end);
            invokeAll(leftTask, rightTask); // 두 작업을 동시에 실행
        }
    }

    public static void main(String[] args) {
        int[] array = {5, 3, 1, 8, 7, 6, 4, 2, 9, 0};
        ForkJoinPool pool = new ForkJoinPool();
        SortTaskExample task = new SortTaskExample(array, 0, array.length);

        pool.invoke(task);
        System.out.println(Arrays.toString(array));
        pool.shutdown();
    }
}
```

<br><br>

---