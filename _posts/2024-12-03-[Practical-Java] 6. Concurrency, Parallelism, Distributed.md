---
layout: post
title:  "[Practical-Java] 6. Concurrency, Parallelism, Distributed"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

병행(Concurrency), 병렬(Parallelism), 분산(Distributed)은 모두 작업을 효율적으로 처리하기 위한 프로그래밍 및 컴퓨팅 모델이며 이 개념들은 서로 관련이 있지만, 목표와 실행 방식에서 차이가 있다.

<br>


# 병행(Concurrency), 병렬(Parallelism), 분산(Distributed)

# 1. 병행 프로그래밍 (Concurrency)

**병행(Concurrency)**은 **여러 작업이 동시에 실행되는 것처럼 보이게 만드는 프로그래밍 기법**입니다. 작업 간의 **논리적 동시성**을 다루며, 실제로는 작업들이 짧은 시간 단위로 스위칭되어 실행될 수도 있습니다.

### 특징

- **논리적으로 동시에 실행**되지만, 물리적으로 병렬로 실행되지 않을 수도 있음.
- 단일 CPU에서도 멀티태스킹처럼 여러 작업이 번갈아가며 실행 가능.
- 주로 **작업 간 상호작용, 공유 자원 관리**에 초점.

### 예제

GUI 프로그램에서 버튼 클릭, 네트워크 요청 처리, 애니메이션 실행 등이 동시에 처리되는 것처럼 보이는 상황.

#### Java 병행성 예제
```java
public class ConcurrencyExample {
    public static void main(String[] args) {
        Runnable task1 = () -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Task 1 - Count: " + i);
            }
        };

        Runnable task2 = () -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Task 2 - Count: " + i);
            }
        };

        Thread thread1 = new Thread(task1);
        Thread thread2 = new Thread(task2);

        thread1.start();
        thread2.start();
    }
}
```

<br><br>


# 2. 병렬 프로그래밍 (Parallelism)

**병렬(Parallelism)**은 **여러 작업을 실제로 동시에 실행**하는 프로그래밍 기법입니다. 이는 멀티코어 CPU나 GPU 같은 하드웨어를 사용하여 이루어집니다.

## 특징

- **물리적으로 동시에 실행됨**:
  여러 작업이 실제로 동시에 실행됩니다.
- **하드웨어 활용**:
  멀티코어 CPU의 코어나 GPU의 프로세싱 유닛을 활용.
- **속도 향상**:
  주로 작업의 속도를 높이는 데 초점.
- **데이터 병렬 처리**:
  같은 작업을 여러 데이터에 병렬로 적용.
- **작업 병렬 처리**:
  서로 다른 작업을 병렬로 실행.

## 예제
- **대규모 배열을 병렬로 처리**.
- 머신러닝 모델 훈련 시 데이터 병렬 처리.

## Java 병렬 스트림 예제

### 코드
```java
import java.util.stream.IntStream;

public class ParallelismExample {
    public static void main(String[] args) {
        // 병렬 스트림으로 숫자 합계 계산
        int sum = IntStream.range(1, 100)
                           .parallel() // 병렬 처리 활성화
                           .sum();

        System.out.println("Sum: " + sum); // 출력: 4950
    }
}
```

<br><br>


# 3. 분산 컴퓨팅 (Distributed Computing)

**분산(Distributed Computing)**은 **여러 대의 독립적인 컴퓨터(노드)를 네트워크로 연결하여 작업을 분산 처리**하는 방식이디. 각 컴퓨터는 독립적으로 작업을 수행하며, 결과를 결합하거나 서로 데이터를 교환힌다.

## 특징

- **물리적으로 분리된 시스템에서 작업 실행**:
  각 컴퓨터(노드)가 독립적으로 작동.
- **네트워크를 통한 통신**:
  작업 분배 및 데이터 공유를 위해 네트워크 연결이 필요.
- **확장성과 신뢰성 향상**:
  추가적인 노드가 쉽게 작업 처리량을 증가시키며, 장애 발생 시에도 복구 가능.
- **대표적인 활용 분야**:
  - 빅데이터 처리.
  - 클라우드 컴퓨팅.
  - 분산 데이터베이스.

## 예제
- **Hadoop**: 분산 파일 시스템 및 데이터 처리 프레임워크.
- **Apache Spark**: 분산 데이터 처리를 위한 빠르고 유연한 시스템.
- **Google MapReduce**: 대규모 데이터 분석을 위한 분산 처리 모델.

## 분산 컴퓨팅 모델
1. **Master-Worker**:
   - 하나의 중앙 서버(Master)가 작업을 분배하고, 결과를 수집.
   - 작업자는(Worker)는 각 작업 단위를 처리하고 결과를 반환.
2. **Peer-to-Peer**:
   - 모든 노드가 동등한 역할을 수행하며, 작업과 데이터를 공유.


## 간단한 분산 처리 예제 (Pseudocode)

### 서버 1

```java
public void processPart1() {
    // Part 1의 데이터를 처리하고 결과를 저장
}
```

### 서버 2

```java
public void processPart2() {
    // Part 2의 데이터를 처리하고 결과를 저장
}
```

### 중앙 서버

```java
public void combineResults() {
    // 서버 1과 서버 2의 결과를 결합
}
```

<br>

---

## 요약

### 병행 프로그래밍 (Concurrency)

- 여러 작업을 **동시에 실행하는 것처럼 보이도록 설계**된 프로그래밍 기법.
- **동기화와 공유 자원 관리**가 핵심.

### 병렬 프로그래밍 (Parallelism)

- 실제로 **여러 작업을 물리적으로 동시에 실행**하여 **성능을 최적화**.

### 분산 컴퓨팅 (Distributed Computing)

- **네트워크로 연결된 여러 컴퓨터**에서 작업을 나누어 처리하여 **확장성과 신뢰성을 제공**.

---

## 활용 사례

### 병행 프로그래밍

- **GUI 응답성** 유지.
- **비동기 네트워크 요청 처리**.

### 병렬 프로그래밍

- **데이터 병렬 처리**.
- **머신러닝** 모델 학습.
- **과학 계산**.

### 분산 컴퓨팅

- **클라우드 서비스**.
- **빅데이터 분석**.
- **분산 데이터 저장소**.