---
layout: post
title:  "[Practical-Java] 12. Parallel Programming-JAVA 8 : ParallelStream"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---


스트림 병렬 처리는 자바(Java) 8 이상에서 도입된 Stream API의 중요한 기능 중 하나로 개발자는 데이터의 병렬 처리를 간단하게 적용할 수 있으며, 멀티 코어 프로세서의 성능을 최대한 활용하여 작업을 더 빠르게 처리할 수 있다.

<br>


# 병렬 프로그래밍 : JAVA 8-스트림 병렬 처리

ParallelStream은 내부적으로 ForkJoinPool을 사용하여 작업을 여러 스레드에 분산시키고, 각 스레드는 데이터의 서브셋을 독립적으로 처리한 다음 결과를 합쳐서 처리한다.

<br>


# 1. 스트림 병렬 처리

스트림 병렬 처리는 데이터 처리 작업을 여러 스레드로 분할하여 동시에 실행하는 것을 말한다. `이를 통해 각 스레드는 데이터의 일부분을 독립적으로 처리하고, 최종적으로 모든 결과가 하나로 합쳐`진다. 자바에서는 `Stream` 인터페이스의 `parallel()` 메소드를 호출하거나, 컬렉션 인터페이스의 `parallelStream()` 메소드를 사용하여 스트림을 병렬 처리 할 수 있다.

<br>


# 2. 스트림 병렬 처리 특징 및 장점

### 스트림 병렬 처리의 특징
1. **`자동 분할과 작업 관리`**: `자바의 ForkJoinPool을 사용하여 자동으로 작업을 분할하고 스레드에 할당`하기에 `개발자는 병렬 실행의 세부적인 스레드 관리를 신경 쓸 필요가 없다.`
2. **비결정적 처리**: 병렬 스트림에서는 처리 순서가 일반적인 순차 스트림과 다를 수 있으며, 동일한 입력에 대해서도 다른 실행마다 다른 순서로 결과가 나올 수 있다.
3. **결합성(associativity)**: 병렬 처리의 정확한 결과를 보장하기 위해, 스트림에서 사용하는 함수는 결합성을 가져야 한다. 즉, 입력의 순서와 상관없이 결과가 동일해야 한다.

### 스트림 병렬 처리의 장점
1. **성능 향상**: 대용량 데이터를 처리할 때 병렬 스트림을 사용하면 처리 시간을 단축시킬 수 있다. 멀티 코어 프로세서의 각 코어가 데이터의 일부를 병렬로 처리하기 때문에, 전반적인 성능이 개선된다.
2. **코드 간소화**: 병렬 처리 로직을 직접 구현하는 것은 복잡하고 유지보수가 어렵지만, 스트림 API를 사용하면 병렬 처리를 몇 줄의 코드로 간단히 적용할 수 있으며, 코드의 가독성과 유지 보수성이 향상된다.
3. **스케일 업**: 애플리케이션이 실행되는 환경의 하드웨어 자원이 개선될 때 자동으로 성능이 향상될 수 있고 더 많은 코어나 스레드를 갖는 시스템에서는 더 많은 작업을 동시에 처리할 수 있다.

### 사용 시 고려해야 할 사항
- **오버헤드**: 병렬 처리는 스레드 간의 데이터 분할과 결과 합치는 과정에서 오버헤드가 발생할 수 있으므로, 작은 데이터셋에 대해서는 오히려 성능이 저하 될 수 있다.
- **스레드 안전성**: 공유된 자원에 대한 접근이나 변경 시에는 스레드 안전성을 고려하여 설계해야 한다.
- **순서 의존성**: 데이터 처리 순서에 의존하는 작업에서는 병렬 스트림이 적합하지 않을 수 있다

<br>


# 3. 스트림 병렬 처리의 작동 방식

스트림 병렬 처리는 크게 다음의 단계로 나눌 수 있다.

1. **병렬 스트림 생성**:
   - 자바에서는 기존의 순차 스트림을 `Stream.parallel()` 메서드를 사용하여 병렬 스트림으로 전환하거나, 컬렉션의 `Collection.parallelStream()` 메서드를 통해 직접 병렬 스트림을 생성할 수 있다.

2. **`데이터 분할 (Decomposition)`**:
   - 병렬 스트림은 내부적으로 데이터를 적절한 크기의 조각으로 분할한다. 이는 보통 데이터 소스의 특성과 코어의 수를 고려하여 자동으로 결정된다.
   - 분할은 `Spliterator`라는 특별한 객체를 통해 수행되며, 이 객체는 데이터를 효과적으로 분할하고 각 조각을 별도의 스레드로 보내 처리할 수 있도록 한다.

3. **`병렬 실행 (Parallel)`**:
   - 각 스레드는 할당받은 데이터 조각에 대해 독립적으로 작업을 수행한다. 예를 들어, 필터, 맵, 리듀스 등의 연산이 병렬로 실행된다.
   - 이 단계는 자바의 `ForkJoinPool`을 활용하여 수행된다.. `ForkJoinPool`은 효율적으로 작업을 스레드에 할당하고, 이들 사이에서 작업 부하를 균형있게 배분한다.

4. **`결과의 합병 (Combination)`**:
   - 스레드에서 수행된 작업 결과는 최종적으로 하나의 결과로 합쳐진다. 이 과정은 연산의 성겨에 따라 다양한 방식으로 이루어지며, 종종 리듀스 연산이 사용된다.
   - 예를 들어, `reduce()` 또는 `collect()` 메서드를 통해 결과를 결합하고 최종적인 출력을 생성한다.
  
<br>


## 기본 ForkJoinPool 사용 병렬 스트림 예제코드

별도의 스레드 풀을 설정하지 않고 자바의 기본적인 병렬 스트림 처리 방식으로 `자바의 ForkJoinPool.commonPool()을 사용하여 자동으로 시스템의 CPU 코어 수에 따라 작업을 분할하고 스레드에 할당`하기에 `개발자는 병렬 실행의 세부적인 스레드 관리를 신경 쓸 필요가 없다.`

```java
List<BankAccount> bankAccountList = getDummyBankAccountList();

List<BankAccount> filteringBankAccountList = bankAccountList.parallelStream()
        .peek(bankAccount -> {
            try {
                System.out.println("# ID: " + bankAccount.getAccountId() + ", ThreadName: " + Thread.currentThread().getName());
                Thread.sleep(2000); // 2초 지연
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt(); // 현재 스레드의 인터럽트 상태를 설정
                throw new RuntimeException(e); // 런타임 예외로 변환하여 던짐
            }
        })
        .filter(bankAccount -> bankAccount.getBalance() < 10000)
        .collect(Collectors.toList());

System.out.println("Filtered Orders: " + filteringBankAccountList.toString());
```

<br>


## 명시적 ForkJoinPool 사용 병렬 스트림 예제코드

명시적으로 ForkJoinPool 객체를 생성하고, 이를 사용하여 병렬 스트림의 스레드 풀 크기를 직접 제어한다. submit() 메서드를 통해 작업을 제출하고, get() 메서드로 결과를 동기적으로 기다린다. 작업 완료 후 shutdown() 메서드로 풀을 종료하여 자원을 정리한다.

```java
List<BankAccount> bankAccountList = getDummyBankAccountList();

ForkJoinPool customThreadPool = new ForkJoinPool(2);
ForkJoinTask<List<BankAccount>> forkJoinTask = customThreadPool.submit(
        () -> bankAccountList.parallelStream()
                .peek(bankAccount -> {
                    try {
                        System.out.println("# ID: " + bankAccount.getAccountId() + ", ThreadName: " + Thread.currentThread().getName());
                        Thread.sleep(2000); // 2초 지연
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt(); // 현재 스레드의 인터럽트 상태를 설정
                        throw new RuntimeException(e); // 런타임 예외로 변환하여 던짐
                    }
                })
                .filter(bankAccount -> bankAccount.getBalance() < 10000)
                .collect(Collectors.toList()));
customThreadPool.shutdown();

List<BankAccount> filteringBankAccountList = forkJoinTask.get();

System.out.println("Filtered Orders: " + filteringBankAccountList.toString());
```

<br><br>


---