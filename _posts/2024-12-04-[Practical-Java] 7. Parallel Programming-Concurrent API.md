---
layout: post
title:  "[Practical-Java] 7. Parallel Programming : Concurrent API"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Java의 java.util.concurrent 패키지 컨커런트 API는 멀티스레드 프로그래밍을 보다 효과적으로 관리하고 구현할 수 있도록 다양한 동기화 클래스와 인터페이스를 제공한다. JDK 5에서 처음 소개되었으며, 그 이후로 여러 개선과 추가 기능이 포함되었습니다. 이 API는 성능을 최적화하고 스레드 관리를 단순화하여 보다 견고한 병렬 실행 애플리케이션을 개발할 수 있도록 설계되었다. 해당 포스팅은 `컨커런트 API의 java.util.concurrent 패키지의 주요 구성 요소`들의 기능고 개념에 대한 내용이다. 

<br>

컨커런트 API는 아래 5가지 특징으로 스레드에서 데이터 정합성을 확보하며 멀티 스레드 환경에서 프로그래밍하게 해준다. 

<br>

# 병렬 프로그래밍 : 컨커런트API

- 병렬 애플리케이션에서 데이터의 동기화와 정합성을 확보하기 위해 Lock 객체를 제공하며 이를 통해 잠금 기능을 사용할 수 있다.
- 스레드를 실행하고 관리하는 고수준 API를 사용한 Executors 클래스를 제공한다. 이 클래스는 Excutor 인터페이스를 구현한 것으로 java.util.concurrent 패키지에 포함되어 있으며 대량 데이터를 병렬처리시에 적합하다.
- 병렬 프로그램에서 대량 데이터의 정합성을 유지한 채 사용하기 위한 컬렉션 프레임워크의 확장관인 컨커런트 컬렉션 클래스를 제공한다.
- 원자적 변수는 동기화를 위한 synchronized 키워드 사용을 최소화하여 성능을 확보하면서 메모리 정합성(Memory consistency) 에러를 방지하는 기능을 제공한다.
- ThreadLocalRandom 클래스를 이용해서 멀티 스레드 환경에서 효율적인 난수를 생성하는 기능을 제공한다.

---

# 1. Executor(실행자)

자바의 컨커런트 API에서 Executor(실행자) 프레임워크는 동시성 프로그래밍을 위한 핵심 구성 요소 중 하나이다. 이 프레임워크는 스레드 생성과 관리를 추상화하고, 태스크 실행을 단순화하여, 개발자가 멀티스레드 애플리케이션을 더 쉽게 구현할 수 있도록 설계되어있다.

<br>

## Executor 인터페이스

`Executor` 인터페이스는 가장 기본적인 실행자 인터페이스로, 단일 메소드 `execute(Runnable command)`를 정의한다. 이 메소드는 `Runnable` 객체를 받아서, 즉시 실행하거나 나중에 실행하기 위해 대기열에 넣습니다. 이 인터페이스의 구현체는 이 태스크를 어떻게 처리할지(즉시 실행, 큐에 넣기, 스레드 풀 사용 등) 결정한다.

<br>

## ExecutorService 인터페이스

`ExecutorService`는 `Executor`를 확장한 더 복잡한 실행자 인터페이스이다. 이 인터페이스는 태스크 실행 뿐만 아니라, 실행자 서비스의 생명주기 관리, 태스크 상태 추적 및 결과 회수 기능을 제공한다. `ExecutorService`는 다음과 같은 중요한 메소드를 제공힌다.

- `submit(Callable<T> task)`: `Callable` 인터페이스를 구현한 태스크를 실행하고, 결과를 `Future<T>`로 반환한다.
- `invokeAll(Collection<? extends Callable<T>> tasks)`: 태스크 컬렉션을 실행하고, 각 태스크의 결과를 담은 `Future` 객체 리스트를 반환한다.
- `shutdown()`: 실행자 서비스를 점진적으로 종료한다. 이미 제출된 태스크는 처리하나, 새로운 태스크는 수락하지 않는다.
- `shutdownNow()`: 실행자 서비스를 즉시 종료하고, 현재 진행 중인 태스크를 중단한다.

<br>

## ScheduledExecutorService 인터페이스

`ScheduledExecutorService`는 `ExecutorService`의 한 형태로, 태스크를 일정 시간 후 또는 정기적으로 실행할 수 있는 기능을 추가한다. 주요 메소드는 다음과 같다.

- `schedule(Callable<V> callable, long delay, TimeUnit unit)`: 지정된 지연 후에 `Callable` 태스크를 실행한다.
- `scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`: 지정된 초기 지연 이후에 주기적으로 `Runnable` 태스크를 실행한다.
- `scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit)`: 각 실행이 완료된 후 정해진 지연을 두고 `Runnable` 태스크를 반복 실행한다.

<br>

## 주요 구현체

- **ThreadPoolExecutor**: 가장 일반적으로 사용되는 `ExecutorService` 구현체로, 태스크를 효율적으로 처리할 수 있는 조정 가능한 스레드 풀을 제공한다.
- **ScheduledThreadPoolExecutor**: `ScheduledExecutorService` 인터페이스를 구현하며, 일정에 따라 태스크를 실행하는 데 최적화된 스레드 풀을 제공한다.

<br>

실행자 프레임워크는 자바의 동시성 프로그래밍을 위한 강력한 도구로, 복잡한 스레드 관리와 태스크 스케줄링을 단순하기에 개발자는 이 프레임워크를 사용함으로써 스레드 관리의 복잡성을 줄이고, 애플리케이션의 성능과 확장성을 향상시킬 수 있다.

<br>

### Executor 인터페이스 예제 코드

```java
import java.util.concurrent.Executor;
import java.util.concurrent.Executors;

public class SimpleExecutorExample {
    public static void main(String[] args) {
        // Executor 인터페이스의 구현체를 생성
        // 여기서는 단일 스레드를 사용하는 Executors의 newSingleThreadExecutor 메서드를 사용
        Executor executor = Executors.newSingleThreadExecutor();

        // Runnable 태스크를 정의
        Runnable task = new Runnable() {
            @Override
            public void run() {
                System.out.println("Executed by: " + Thread.currentThread().getName());
            }
        };

        // Executor를 통해 태스크를 실행
        executor.execute(task);

        // ExecutorService를 안전하게 종료하기 위한 코드
        // ExecutorService로 캐스팅하여 shutdown 메서드를 호출
        if (executor instanceof ExecutorService) {
            ((ExecutorService) executor).shutdown();
        }
    }
}
```

<br><br>


# 2. Queue(큐)

자바 컨커런트 API에서 Queue(큐)는 멀티스레드 환경에서 데이터를 효율적으로 교환하고 관리할 수 있도록 설계된 중요한 선입선출(FIFO)데이터 구조로 `java.util.concurrent` 패키지는 표준 `Queue` 인터페이스를 확장하고 강화하여 여러 스레드 간의 데이터 전달을 위한 안전하고 빠른 방법을 제공한다.

<br>

## BlockingQueue 인터페이스

- `BlockingQueue`는 `Queue`의 한 형태로, 요소를 추가하거나 제거할 때 스레드를 블록할 수 있는 기능을 추가한다.
- 생산자-소비자 문제를 해결하는 데 매우 유용하다.
- 주요 메소드에는 `put()` (큐가 가득 차면 블록) 및 `take()` (큐가 비어 있으면 블록)가 포함된다.


## ConcurrentLinkedQueue 클래스

- 락-프리 알고리즘을 사용하여 구현된 비블로킹 큐이다.
- 동시성이 높은 환경에서 요소를 빠르게 추가하고 제거할 수 있다.
- 큐가 빈 상태에서의 폴(poll) 연산은 `null`을 반환하며 블록하지 않는다.


## LinkedBlockingQueue 클래스

- 선택적으로 용량을 설정할 수 있는 블로킹 큐이다.
- 내부적으로 단일 링크 노드를 사용하여 요소를 관리한다.
- 고정된 용량을 갖지 않는 경우 큐는 기본적으로 `Integer.MAX_VALUE`의 크기를 갖는다.

## ArrayBlockingQueue 클래스

- 배열 기반의 유한 블로킹 큐 구현체이다.
- 큐의 크기를 생성 시점에 지정해야 한다.
- 공정성 정책을 설정할 수 있어, 대기 중인 스레드가 FIFO(First-In-First-Out) 순서로 접근할 수 있다.


## PriorityBlockingQueue 클래스

- 우선 순위를 고려하는 블로킹 큐이다.
- 요소는 자연 순서 또는 Comparator에 따라 우선 순위를 결정한다.
- 내부적으로 힙(heap)을 사용하여 요소를 관리하며, 용량 제한이 없다.

## SynchronousQueue 클래스

- 큐에 단일 요소만 저장할 수 있는 특별한 종류의 블로킹 큐이다.
- 요소를 추가하려면 반드시 다른 스레드가 그 요소를 동시에 가져가야 한다.
- 내부적으로 아무 요소도 저장하지 않고, 단지 스레드 간의 핸드오프(handing-off)를 통해 동작한다.

<br>

## 주요 큐 클래스 및 인터페이스의 메서드

| 클래스/인터페이스             | 메서드                 | 반환 타입 | 설명                                                                                                 | 블로킹 여부 |
|---------------------------|----------------------|-------|-----------------------------------------------------------------------------------------------------|--------|
| **BlockingQueue**         | `void put(E e)`      | void  | 지정된 요소를 큐에 추가하며,<br>필요한 경우 공간이 생길 때까지 스레드를 블로킹합니다.                               | 예      |
| **BlockingQueue**         | `E take()`           | E     | 큐의 헤드에서 요소를 검색하고 제거하며,<br>필요한 경우 요소가 사용 가능해질 때까지 스레드를 블로킹합니다.                  | 예      |
| **ConcurrentLinkedQueue** | `boolean offer(E e)` | boolean | 지정된 요소를 큐의 꼬리에 추가하며,<br>성공하면 true를 반환하고, 큐가 가득 찼으면 false를 반환합니다.                 | 아니오   |
| **ConcurrentLinkedQueue** | `E poll()`           | E     | 큐의 헤드에서 요소를 검색하고 제거하며,<br>큐가 비어 있으면 null을 반환합니다.                                    | 아니오   |
| **LinkedBlockingQueue**   | `void put(E e)`      | void  | 지정된 요소를 큐에 추가하며,<br>필요한 경우 공간이 생길 때까지 스레드를 블로킹합니다.                               | 예      |
| **LinkedBlockingQueue**   | `E take()`           | E     | 큐의 헤드에서 요소를 검색하고 제거하며,<br>필요한 경우 요소가 사용 가능해질 때까지 스레드를 블로킹합니다.                  | 예      |
| **ArrayBlockingQueue**    | `boolean offer(E e)` | boolean | 지정된 요소를 큐에 추가하려고 시도하며,<br>성공하면 true를 반환하고, 큐가 가득 찼으면 false를 반환합니다.             | 아니오   |
| **ArrayBlockingQueue**    | `E poll()`           | E     | 큐의 헤드에서 요소를 검색하고 제거하며,<br>큐가 비어 있으면 null을 반환합니다.                                    | 아니오   |
| **ArrayBlockingQueue**    | `void put(E e)`      | void  | 지정된 요소를 큐에 추가하며,<br>필요한 경우 공간이 생길 때까지 스레드를 블로킹합니다.                               | 예      |
| **ArrayBlockingQueue**    | `E take()`           | E     | 큐의 헤드에서 요소를 검색하고 제거하며,<br>필요한 경우 요소가 사용 가능해질 때까지 스레드를 블로킹합니다.                  | 예      |
| **PriorityBlockingQueue** | `boolean offer(E e)` | boolean | 지정된 요소를 큐의 꼬리에 추가합니다.<br>용량 제한이 없기 때문에 항상 true를 반환합니다.                             | 아니오   |
| **PriorityBlockingQueue** | `E poll()`           | E     | 큐의 헤드에서 요소를 검색하고 제거하며,<br>큐가 비어 있으면 null을 반환합니다.                                    | 아니오   |
| **SynchronousQueue**      | `void put(E e)`      | void  | 지정된 요소를 큐에 추가하며,<br>다른 스레드가 해당 요소를 받을 때까지 스레드를 블로킹합니다.                           | 예      |
| **SynchronousQueue**      | `E take()`           | E     | 다른 스레드가 큐에 요소를 넣을 때까지 기다립니다.<br>요소가 제공되면 그 요소를 반환하고, 큐는 다시 비게 됩니다.             | 예      |


<br>

### ArrayBlockingQueue 인터페이스 예제 코드

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

public class SimpleArrayBlockingQueueExample {

    public static void main(String[] args) {

        // ArrayBlockingQueue 에 용량 5로 초기화
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);

        // 생산자 스레드
        Thread producer = new Thread(() -> {
            String[] events = {"First", "Second", "Third", "Fourth", "Fifth"};
            try {
                for (String event : events) {
                    queue.put(event); // 큐가 가득 찰 경우 블록됨
                    System.out.println("Produced: " + event);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        // 소비자 스레드
        Thread consumer = new Thread(() -> {
            try {
                while (!Thread.currentThread().isInterrupted()) {
                    String event = queue.take(); // 큐가 비었을 경우 블록됨
                    System.out.println("Consumed: " + event);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();

        // 스레드를 종료하기 위한 코드..
    }
}
```

<br>


### LinkedBlockingQueue 인터페이스 예제 코드

```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

public class SimpleLinkedBlockingQueueExample {

    public static void main(String[] args) {

        // LinkedBlockingQueue 인스턴스 생성
        BlockingQueue<String> queue = new LinkedBlockingQueue<>();

        // 생산자 스레드 생성 및 실행
        Thread producer = new Thread(() -> {
            String[] events = {"Event-1", "Event-2", "Event-3", "Event-4", "Event-5", "Event-6", "Event-7"};
            try {
                for (String event : events) {
                    queue.put(event);  // 큐에 이벤트 추가, 큐가 가득 차면 블록
                    System.out.println("Produced: " + event);
                }
                queue.put("END");  // 종료 신호로 "END" 이벤트 추가
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        // 소비자 스레드 생성 및 실행
        Thread consumer = new Thread(() -> {
            try {
                while (true) {
                    String event = queue.take();  // 큐에서 이벤트 제거, 큐가 비어 있으면 블록
                    if ("END".equals(event)) {
                        break;  // "END" 이벤트 수신 시 종료
                    }
                    System.out.println("Consumed: " + event);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}
```

<br>

이러한 큐들은 멀티스레드 프로그램에서 데이터를 안전하게 교환하고, 스레드 간 동기화 문제를 최소화하며, 데이터 처리 속도를 최적화하는 데 큰 도움을 줍니다. 특히 생산자-소비자 패턴, 워커 스레드 풀 관리, 이벤트 처리 시스템 등 다양한 동시성 디자인 패턴에서 활용된다.

자바의 컨커런트 큐는 멀티스레드 애플리케이션에서의 데이터 처리와 스레드 관리를 효과적으로 지원하여, 복잡하고 동시성이 요구되는 환경에서도 성능과 안정성을 보장한다.

<br><br>


# 3. Timing(타이밍)

자바 컨커런트 API에서 타이밍 기능은 주로 스레드의 실행을 예약하거나 일정 시간 동안 스레드를 지연시키는 데 사용됩니다. 이 기능은 `ScheduledExecutorService` 인터페이스를 통해 제공되며, 정해진 시간에 따라 작업을 실행하거나 반복 실행할 수 있도록 합니다. 또한, 다양한 메서드들을 통해 작업의 실행 시간을 정밀하게 제어할 수 있다. 그래서 타임아웃 기능을 통해 불필요한 스레드, 좀비나 데드록 스레드를 관리함으로서 소프트웨어를 안정적으로 관리한다.

<br>

## ScheduledExecutorService 인터페이스

`ScheduledExecutorService`는 `ExecutorService`를 확장한 인터페이스로, 스레드를 예약하여 실행하는 기능을 추가로 제공합니다. 이 인터페이스는 타이밍을 기반으로 작업을 관리하는 여러 메서드를 포함합니다:

1. **`schedule(Callable<V> callable, long delay, TimeUnit unit)`**:
   - 지정된 지연 후에 `Callable` 작업을 한 번 실행합니다.
   - 결과를 `Future<V>`로 반환하여 나중에 결과를 검색할 수 있습니다.

2. **`schedule(Runnable command, long delay, TimeUnit unit)`**:
   - 지정된 지연 후에 `Runnable` 작업을 한 번 실행합니다.
   - 반환 값이 없는 작업에 적합합니다.

3. **`scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`**:
   - 초기 지연 후에 작업을 시작하고, 이후 주어진 시간 간격마다 작업을 반복 실행합니다.
   - 이 메서드는 실제 실행 시간을 기반으로 다음 실행 시간을 계산합니다.

4. **`scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit)`**:
   - 초기 지연 후에 작업을 시작하고, 각 실행이 끝난 후 지정된 지연을 기다린 후 작업을 반복 실행합니다.
   - 이 메서드는 작업의 실행 완료 시간을 기반으로 다음 실행 시간을 계산합니다.

<br>

## 타이밍과 관련된 기능의 중요성

타이밍 기능은 애플리케이션에서 주기적으로 실행해야 하는 유지보수 작업, 로그 처리, 데이터 백업 같은 작업을 스케줄링하는 데 매우 중요합니다. 또한, 일정 시간 간격으로 폴링이나 상태 체크를 수행해야 하는 경우에도 유용하게 사용됩니다.

<br>

### ScheduledExecutorService 스케줄링된 작업 실행 예제 코드

```java
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ScheduledTaskExample {
    public static void main(String[] args) {
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

        Runnable task = () -> System.out.println("Task executed at: " + System.currentTimeMillis());

        // 5초 후에 작업 실행
        scheduler.schedule(task, 5, TimeUnit.SECONDS);

        // 10초 후에 시작하여 3초마다 반복 실행
        scheduler.scheduleAtFixedRate(task, 10, 3, TimeUnit.SECONDS);

        // 애플리케이션 종료 시 스케줄러 종료
        scheduler.schedule(() -> {
            System.out.println("Scheduler shutting down.");
            scheduler.shutdown();
        }, 60, TimeUnit.SECONDS);
    }
}
```

<br>

## 결론

위 예제에서는 `ScheduledExecutorService`를 사용하여 단일 스레드에서 여러 작업을 예약하는 예제코드이며, 이처럼 자바의 컨커런트 API를 활용한 타이밍 기능은 정밀한 시간 관리와 효율적인 리소스 활용을 가능하게 하며, 복잡한 타이밍 요구 사항을 갖는 애플리케이션 개발에 필수적이다.

<br><br>


# 4. 동기화(Synchronizers)

자바의 `java.util.concurrent` 패키지는 멀티스레드 프로그램에서 동기화를 위한 다양한 고급 도구를 제공합니다. 이 도구들은 스레드 간의 작업 흐름을 조율하거나, 데이터를 교환하고, 스레드 작업을 동기화하는 데 사용됩니다. 주로 사용되는 동기화 기구는 `Semaphore`, `CountDownLatch`, `CyclicBarrier`, `Phaser`, 그리고 `Exchanger` 입니다.

<br>

## Semaphore (세마포어)

- **개념**: 세마포어는 한정된 리소스를 여러 스레드가 공유할 때 사용하는 카운팅 기반의 동기화 메커니즘
- **용도**: `특정 리소스 또는 섹션에 동시에 접근할 수 있는 스레드의 수를 제한`
- **메소드**:
  - `acquire()`: 리소스를 사용하고자 할 때 호출하며, 세마포어가 0이 아니면 접근을 허용하고, 0이면 대기
  - `release()`: 리소스 사용이 끝났을 때 호출하여 세마포어의 카운트를 증가시키고, 대기 중인 스레드에게 신호를 보냄

> 이러한 방식은 멀티 스레드 환경에서 리소스의 동기화와 스레드의 안전한 작업 수행을 보장하는 데 중요하다.

```java
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

/**
 * Semaphore(세마포어)를 활용하여 리소스에 대한 동시 접근을 제어하는 방법을 보여주는 예제 코드이다.
 * Semaphore 를 사용하여 리소스를 안전하게 공유하면서도 동시에 하나의 스레드만 작업을 수행하게 함으로써,
 * 리소스 충돌이나 데드락을 방지할 수 있다.
 */
public class SimpleSyncSemaphoreExample {

    public static void main(String[] args) {

        //최대 1초 동안 세마포어 허용을 시도합니다. 허용을 얻으면 true를 반환하고, 실패하면 false를 반환
        Semaphore semaphore = new Semaphore(1);  // 1개의 허용을 가진 세마포어

        Runnable longRunningTask = () -> {
            boolean permit = false;
            try {
                permit = semaphore.tryAcquire(1, TimeUnit.SECONDS);
                if (permit) {
                    System.out.println("Semaphore acquired");
                    Thread.sleep(5000); // 긴 작업 시뮬레이션
                } else {
                    System.out.println("Could not acquire semaphore");
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            } finally {
                if (permit) {
                    semaphore.release();
                    System.out.println("Semaphore released");
                }
            }
        };

        new Thread(longRunningTask).start();
        new Thread(longRunningTask).start();
    }
}
```

<br>

## CountDownLatch(카운트 다운 래치)

- **개념**: 일정 수의 이벤트가 발생할 때까지 다른 스레드들이 대기하도록 하는 데 사용
- **용도**: `하나 이상의 스레드가 특정 작업들이 완료될 때까지 기다려야 할 필요가 있을 때 사용`
- **메소드**:
  - `countDown()`: 이벤트가 발생하거나 작업이 완료될 때 호출되어 래치의 카운트를 감소
  - `await()`: 래치의 카운트가 0이 될 때까지 대기

> 멀티스레드 프로그램에서 모든 스레드의 실행이 완료된 후에만 특정 작업을 수행하도록 하고자 할 때 유용하다.

```java
import java.util.concurrent.CountDownLatch;

/**
 * CountDownLatch 를 사용하여 동기화 기법을 구현한 예제 코드이다.
 * 이 클래스는 여러 스레드가 동시에 실행되고 작업이 모두 완료할 때까지 기다리게 하는 데 사용되며,
 * 주로 멀티 스레드 프로그램에서 모든 스레드의 작업 완료를 동기화하는 데 적합하다.
 */
public class SimpleSyncCountDownLatchExample {

    public static void main(String[] args) {

        final int count = 3;    //스레드 갯수
        CountDownLatch latch = new CountDownLatch(count);

        for (int i = 1; i <= count; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + " started.");
                try {
                    Thread.sleep(1000);  // 시뮬레이션을 위해 각 스레드를 1초 동안 지연
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                //latch.countDown(); 호출로 래치 카운트를 감소시키며
                //모든 스레드가 이 메서드를 호출하고 "finished" 메시지를 출력하면 래치 카운트는 0이 된다.
                latch.countDown();  // 래치 카운트를 감소
                System.out.println(Thread.currentThread().getName() + " finished.");
            }).start();
        }

        try {
            //#래치 대기
            //메인 스레드는 latch.await();를 호출하여 모든 스레드가 작업을 완료할 때까지 대기
            //모든 스레드가 countDown()을 호출하여 카운트가 0이 되면, await() 메서드 다음의 코드가 실행되어 "All threads have finished." 메시지를 출력
            latch.await();  // 모든 스레드가 완료될 때까지 대기
            System.out.println("All threads have finished.");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

<br>

## CyclicBarrier (사이클릭 배리어)

- **개념**: 일정 수의 스레드들이 서로 도달할 때까지 모든 스레드가 서로 기다림
- **용도**: `여러 스레드가 작업의 특정 지점에서 동시에 도달해야 할 때 사용`
- **메소드**:
  - `await()`: 모든 스레드가 이 메소드를 호출할 때까지, 즉 배리어 포인트에 모두 도달할 때까지 모든 스레드를 블로킹

> 그래서 이 기법은 복잡한 멀티스레드 애플리케이션에서 특정 작업 단계가 완료되기를 모든 스레드가 기다릴 필요가 있을 때 유용하게 사용한다.

```java
package com.modern.java.practical.executor.example;

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

/**
 * CyclicBarrier 를 사용하여 멀티스레드 프로그래밍에서 동기화를 구현한 예제 코드이다.
 * CyclicBarrier 는 주어진 수의 스레드들이 모두 특정 지점에 도달할 때까지 기다리게 한 후,
 * 모든 스레드가 도착하면 지정된 작업을 실행하는 동기화 기구이다.
 * 이 예제는 세 개의 스레드가 각각 장벽에 도달하고, 모두 도달했을 때 추가 작업을 수행하는 과정을 보여준다.
 */
public class SimpleCyclicBarrierExample {

    public static void main(String[] args) {

        final int count = 3;

        //첫 번째는 파라미터는 장벽을 통과하기 위해 필요한 스레드 수(count)
        //두 번째는 모든 스레드가 장벽에 도달했을 때 실행될 작업
        CyclicBarrier barrier = new CyclicBarrier(count, () -> {
            // 모든 스레드가 도달하면 실행될 작업
            System.out.println("All parties are arrived at barrier, let's play");
        });

        //for 반복문을 사용하여 세 개의 스레드를 생성하고 시작
        //각 스레드는 CyclicBarrier 의 await() 메소드를 호출하여 장벽에서 대기
        //스레드는 "is waiting at barrier" 메시지를 출력하고, await() 호출을 통해 다른 스레드들도 장벽에 도달할 때까지 대기
        //모든 스레드가 장벽에 도달하면, CyclicBarrier 에 설정된 작업이 실행되고,
        //각 스레드는 "has crossed the barrier" 메시지를 출력한 후 await() 메소드로부터 반환된다.
        for (int i = 0; i < count; i++) {
            new Thread(() -> {
                try {
                    System.out.println(Thread.currentThread().getName() + " is waiting at barrier");
                    barrier.await();
                    System.out.println(Thread.currentThread().getName() + " has crossed the barrier");
                } catch (InterruptedException | BrokenBarrierException e) {
                    Thread.currentThread().interrupt();
                }
            }).start();
        }
    }
}
```

<br>

## Phaser (페이저)

- **개념**: CyclicBarrier와 비슷하지만, 더 유연하고 다단계 동기화가 가능
- **용도**: 다단계 계산 작업에서 여러 단계를 거쳐야 할 때 사용
- **메소드**:
  - `arrive()`: 현재 단계를 완료했음을 알리고 다음 단계로 진행
  - `awaitAdvance(int phase)`: 현재 단계가 완료될 때까지 대기

> Phaser는 각 단계에서 모든 스레드가 작업을 완료할 때까지 기다리게 하고, 모든 스레드가 다음 단계로 동시에 넘어갈 수 있도록 합니다. 이러한 특성은 복잡한 멀티스레드 작업에서 매우 유용하게 활용

```java
import java.util.concurrent.Phaser;

/**
 * Phaser 는 자바 컨커런트 API에서 제공하는 동기화 기구 중 하나로,
 * CyclicBarrier와 CountDownLatch의 기능을 합친 것과 유사하다.
 * Phaser 는 다단계 계산에서 여러 단계를 거쳐야 할 때 유용하게 사용될 수 있으며
 * 각 단계에서 특정 수의 스레드가 작업을 완료할 때까지 기다린 후 다음 단계로 넘어갈 수 있게 도와준다.
 */
public class SimpleSyncPhaserExample {

    public static void main(String[] args) {

        //new Phaser(1)은 메인 스레드가 이미 참여하고 있음을 의미. 초기 참가자 수를 '1'로 설정하여 현재(메인) 스레드를 포함
        final Phaser phaser = new Phaser(1);

        System.out.println("Starting");

        // 세 개의 스레드 생성 및 시작
        for (int i = 0; i < 3; i++) {
            int finalI = i + 1;
            Thread thread = new Thread(() -> {
                phaser.register();  // 스레드를 페이저에 등록, 각 스레드가 Phaser의 단계 관리에 포함

                //각 스레드는 3개의 단계(0, 1, 2)를 순차적으로 실행하면서 각 단계가 끝날 때마다
                //phaser.arriveAndAwaitAdvance()를 호출하여 다음 단계로 넘어가기 전에 모든 스레드의 단계 완료를 기다림
                for (int phase = 0; phase < 3; phase++) {
                    System.out.println("Thread " + finalI + " doing work for phase " + phase);
                    phaser.arriveAndAwaitAdvance();  // 현재 단계를 완료하고 다음 단계 전에 기다림
                }

                //각 스레드는 모든 단계를 완료한 후 phaser.arriveAndDeregister()를 호출하여 자신을 Phaser에서 해제
                //메인 스레드는 phaser.isTerminated()가 true가 될 때까지 (즉, 모든 스레드가 Phaser에서 해제될 때까지)
                //phaser.arriveAndAwaitAdvance()를 반복 호출하여 각 단계의 완료를 기다림
                phaser.arriveAndDeregister(); // 완료 후 등록 해제
            });
            thread.start();
        }

        // 모든 스레드의 단계가 끝날 때까지 기다리고 마지막 등록 해제
        while (!phaser.isTerminated()) {
            phaser.arriveAndAwaitAdvance(); // 메인 스레드가 각 단계를 완료하기를 기다림
        }
        System.out.println("All phases are completed.");
    }
}
```

<br>

## Exchanger (교환기)

- **개념**: 두 스레드가 데이터를 교환할 수 있도록 해주는 동기화 포인트
- **용도**: 두 작업 스레드가 데이터를 교환해야 할 때 사용
- **메소드**:
  - `exchange(V x)`: 데이터를 교환하려는 스레드가 호출하여, 다른 스레드가 교환을 위해 호출할 때까지 대기하고, 두 스레드의 데이터를 교환한다.

> Exchanger 는 스레드 간의 데이터 교환을 위한 강력하고 유용한 도구로 특히 두 스레드가 서로 정보를 교환하고 동기화해야 할 경우에 적합하며, 데이터의 일관성과 정확성을 유지하면서 효율적인 스레드 간 커뮤니케이션을 가능

```java
import java.util.concurrent.Exchanger;

/**
 * Exchanger 는 자바의 java.util.concurrent 패키지에 있는 동기화 도구로,
 * 두 스레드 간에 데이터를 교환할 수 있도록 설계되었으며
 * 이 도구는 두 스레드가 데이터를 교환할 준비가 될 때까지 블록되도록 하여, 스레드 간의 안정적인 데이터 전달을 보장한다.
 * Exchanger 는 주로 사용자 상태, 버퍼 등을 교환하거나 재사용하는 데 유용하다.
 */
public class SimpleSyncExchangerExample {

    public static void main(String[] args) {
        Exchanger<String> exchanger = new Exchanger<>();

        //exchange(V x): 현재 스레드가 다른 스레드로부터 객체를 받을 때까지 기다리고 교환에 성공하면 상대 스레드로부터 받은 객체를 반환
        //exchange() 메서드를 호출할 때 자신의 데이터를 인자로 전달하고, 다른 스레드로부터 데이터를 받는다.
        //이 과정에서 두 스레드 모두 exchange() 메서드에서 블로킹될 수 있으며, 서로 교환할 준비가 되면 블로킹이 해제되고 데이터 교환이 이루어진다.

        // 스레드 A
        new Thread(() -> {
            try {
                String message = "Message from Thread A";
                // 스레드 A가 보낸 메시지와 스레드 B로부터 받은 메시지를 교환
                String response = exchanger.exchange(message);
                System.out.println("Thread A received: " + response);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }).start();

        // 스레드 B
        new Thread(() -> {
            try {
                String message = "Message from Thread B";
                // 스레드 B가 보낸 메시지와 스레드 A로부터 받은 메시지를 교환
                String response = exchanger.exchange(message);
                System.out.println("Thread B received: " + response);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }).start();
    }
}
```

<br><br>


# 5. Memory Consistency(메모리 정합성)

메모리 정합성(Memory Consistency)은 멀티스레드 환경에서 메모리의 일관성과 예측 가능한 상호 작용을 보장하는 개념이다. 이는 프로그램이 다양한 스레드에서 변수들에 접근하고 수정할 때 발생할 수 있는 예측하지 못한 행동들을 관리하는 것과 관련이 있다. 다른 스레드들 간의 데이터 공유와 동기화를 제대로 관리하지 않으면, 프로그램의 실행 결과가 비결정적이거나 잘못된 데이터를 생성할 수 있다.

<br>

### 메모리 정합성 모델

메모리 정합성 모델은 멀티 프로세싱 시스템에서 메모리 작업(읽기, 쓰기)의 순서를 정의하며 다음은 몇 가지 중요한 메모리 정합성 모델이다.

1. **엄격 일관성(Strict Consistency)**
   - 모든 스레드는 모든 메모리 작업을 동일한 순서로 보게 된다.
   - 가장 엄격하고 이상적인 모델이지만, 실제 시스템에서 구현하기는 매우 어렵다.

2. **순차 일관성(Sequential Consistency)**
   - 모든 스레드에 대한 메모리 작업이 일정한 순서로 발생하는 것처럼 보인다.
   - 프로그램은 마치 단일 프로세서에서 실행되는 것처럼 작동해야 하며, 모든 스레드는 이 순서를 동일하게 관찰해야 한다.

3. **약한 일관성(Weak Consistency)**
   - 일반적인 메모리 접근은 엄격한 순서를 따르지 않으며, 동기화 작업 전후로만 일관성이 유지된다.
   - 데이터 공유가 빈번한 응용 프로그램에서 성능을 향상시킬 수 있다.

4. **릴리스 일관성(Release Consistency)**
   - 약한 일관성을 더 발전시킨 모델로, 동기화 명령어를 'acquire'와 'release'로 나누어 처리한다.
   - 스레드가 변수를 읽기 전에 'acquire'를, 쓰기 후에 'release'를 호출해야 한다.


### 메모리 장벽(Memory Barriers)

멀티 프로세서 시스템에서 메모리 정합성을 유지하기 위해 메모리 장벽이라는 기술을 사용한다. 메모리 장벽은 특정 포인트에서 스레드의 메모리 작업(읽기 또는 쓰기)을 순서대로 처리하도록 강제하며 이는 하드웨어 레벨에서 제공되기도 하며, 소프트웨어에서 명시적으로 조작할 수도 있다.


### 자바의 메모리 모델

자바에서는 자바 메모리 모델(JMM)을 통해 메모리 정합성 문제를 다루며 JMM은 변수의 가시성, 순서, 원자성 등을 정의하며, 다음과 같은 특징을 포함한다.

- **변수 가시성**: 한 스레드에서 수정한 변수의 값을 다른 스레드가 볼 수 있도록 보장합니다.
- **재배치 금지**: 컴파일러와 프로세서는 코드의 실행 순서를 변경(재배치)할 수 있지만, JMM 규칙을 준수하여야 합니다.
- **volatile 키워드**: volatile로 선언된 변수는 모든 스레드에게 즉시 가시적이며, 변수에 대한 읽기와 쓰기가 메모리 장벽처럼 작동합니다.

<br>


## volatile, synchronized 키워드 예제 설명

Java에서 메모리 정합성은 멀티스레딩 환경에서 중요한 개념으로, 스레드 간의 공유 변수에 대한 변경 사항이 모든 스레드에게 올바르게 보이도록 보장하는 것을 말한다. 이를 위해 Java는 `volatile` 키워드, `synchronized` 블록, 그리고 `java.util.concurrent` 패키지의 다양한 동기화 메커니즘들을 제공한다.

- **`volatile` 키워드**: 이 키워드는 변수를 메인 메모리에서 읽고 쓰도록 강제하며, 캐시를 거치지 않습니다. 이를 통해 모든 스레드가 항상 최신의 값을 보게 된다.
- **`synchronized` 블록**: synchronized 키워드를 사용하면 객체의 모니터 락을 획득하고, 블록을 실행하는 동안 다른 스레드는 이 락을 획득할 수 없습니다. 또한, 블록에 들어가기 전과 나올 때 메모리 가시성을 보장한다.

```java
package com.modern.java.practical.executor.example;

class SharedObject {
    private volatile int volatileCounter = 0;
    private int synchronizedCounter = 0;
    
    //volatile 변수를 사용한 메소드
    //메모리 가시성은 보장하지만 "경쟁 상태(race condition)"로 원장성을 보장하지 못함
    //=> 복잡한 계산이 아닌 간단한 상태감시 등 데이터 변경 사용
    public void incrementVolatileCounter() {
        volatileCounter++;  // 직접 메모리에서 읽고 쓰기 때문에 모든 스레드에게 가시적
    }

    //synchronized 블록을 사용한 메소드
    //incrementSynchronizedCounter 메소드는 스레드가 메소드에 진입할 때 락(lock)을 획득하고, 메소드를 완료할 때 락을 해제
    //=> 가시성과 원장성을 보장
    public synchronized void incrementSynchronizedCounter() {
        synchronizedCounter++;  // 이 메소드는 동기화되어 있으므로 한 번에 하나의 스레드만 접근 가능
    }

    public int getVolatileCounter() {
        return volatileCounter;
    }

    public int getSynchronizedCounter() {
        return synchronizedCounter;
    }
}

public class SimpleSyncMemoryConsistencyExample {

    public static void main(String[] args) throws InterruptedException {
        final SharedObject sharedObject = new SharedObject();
        Runnable incrementTask = () -> {
            for (int i = 0; i < 10000; i++) {
                sharedObject.incrementVolatileCounter();
                sharedObject.incrementSynchronizedCounter();
            }
        };

        Thread t1 = new Thread(incrementTask);
        Thread t2 = new Thread(incrementTask);
        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Volatile counter value: " + sharedObject.getVolatileCounter());
        System.out.println("Synchronized counter value: " + sharedObject.getSynchronizedCounter());
    }
}
```

`volatile`는 메모리 가시성은 보장하지만 "경쟁 상태(race condition)"로 원장성을 보장하지 못한다. 그래서 복잡한 계산이 아닌 간단한 상태감시 등 데이터 변경 사용한다. `synchronized`는 메소드는 스레드가 메소드에 진입할 때 락(lock)을 획득하고, 메소드를 완료할 때 락을 해제하기에 가시성과 원장성을 보장할 수있다.

<br>

## volatile 키워드 가시성 예제 설명

```java
package com.modern.java.practical.executor.example;

/**
 * volatile 변수를 사용하여 status 라는 변수를 두 스레드 간의 상태 정보를 공유
 */
public class SimpleSyncVolatileExample {

    private volatile boolean status = true;  // 시작 상태는 true (계속 실행)

    public void execute() {
        Thread stateMonitor = new Thread(() -> {
            while (status) {  // status가 true인 동안 계속 루프
                // 여기에는 실제 작업 코드가 들어갈 수 있습니다.
                System.out.println("StateMonitor: Status Success");
            }
            System.out.println("StateMonitor: Status changed to false, stopping monitor.");
        });

        Thread stateChanger = new Thread(() -> {
            try {
                Thread.sleep(2000);  // 2초 대기
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            status = false;  // status를 false로 변경
            System.out.println("StateChanger: Status set to false.");
        });

        stateMonitor.start();
        stateChanger.start();
    }

    public static void main(String[] args) {
        new SimpleSyncVolatileExample().execute();
    }
}
```

<br>

## 가시성(Visibility)

`가시성(Visibility)`이란 멀티스레딩 프로그래밍에서 매우 중요한 개념입니다. 특히, 여러 스레드가 같은 데이터에 접근할 때 한 스레드에서 데이터가 변경된 후 다른 스레드들이 그 변경 사항을 언제, 어떻게 볼 수 있는지를 정의합니다. 즉, 한 스레드에서 변수를 수정했을 때 다른 스레드가 그 수정된 값을 "볼 수 있는" 상태가 되는 것을 가리킨다. 멀티스레드 환경에서 가시성 문제가 해결되지 않으면, 스레드들은 메모리의 일관성 없는 상태를 볼 수 있기에 가시성은 중요하다.


### Java에서 가시성 보장 방법

1. **`volatile` 키워드**: 이 키워드는 변수가 각 스레드의 로컬 메모리가 아닌 주 메모리에서 직접 읽히고 쓰여지게 함으로써 모든 스레드에 가시성을 보장
2. **`synchronized` 키워드**: synchronized 블록 또는 메소드를 벗어날 때, 블록 내에서 변경된 모든 변수의 최신 값이 주 메모리에 쓰여지고, 다른 스레드가 이 블록에 진입할 때는 주 메모리에서 변수의 최신 값을 읽어 로컬 메모리에 저장
3. **`final` 키워드**: 객체의 참조가 final로 선언되면, 생성자가 완료된 후에 다른 스레드는 항상 초기화된 최종 필드의 값을 볼 수 있다.
4. **Locks**: `java.util.concurrent` 패키지의 락을 사용하면 락을 획득하는 스레드가 이전에 락을 가지고 있던 스레드가 만든 모든 메모리 변경사항을 볼 수 있다.

<br>


## 원자성(Atomicity)

`원자성(Atomicity)`은 프로그래밍에서 매우 중요한 개념으로, 특히 멀티스레드 환경에서 데이터의 일관성을 유지하기 위해 필수적입니다. 원자성이란 어떤 작업이나 연산이 완전히 수행되거나 전혀 수행되지 않는 것을 보장하는 성질을 말합니다. 연산이 중간 상태에서 중단되지 않고 완전하게 완료되어야 함을 의미(데이터 무결성 보장)합니다.

### Java에서 원자성을 보장하는 방법

1. **Synchronized 키워드**: Java에서 `synchronized` 블록은 임계 영역을 생성하여 한 번에 하나의 스레드만 접근할 수 있게 함으로써 연산의 원자성을 보장
2. **Locks**: `java.util.concurrent.locks` 패키지의 명시적 락을 사용하여 비슷한 방식으로 원자성을 보장
3. **Atomic 클래스**: `java.util.concurrent.atomic` 패키지는 원자성을 보장하는 다양한 클래스(예: `AtomicInteger`, `AtomicLong`)를 제공한다. 이 클래스들은 원자적으로 값을 업데이트하는 메서드를 제공

<br><br>
