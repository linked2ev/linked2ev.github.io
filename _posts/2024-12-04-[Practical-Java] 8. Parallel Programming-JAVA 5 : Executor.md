---
layout: post
title:  "[Practical-Java] 8. Parallel Programming-JAVA 5 : Executor"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

컨커런트 API의 java.util.concurrent 패키지에서 JAVA 5에서 추가 된 **`Executor(실행자)`**에 대한 포스팅이다.

<br>


# 병렬 프로그래밍 : 자바 5-Executor(실행자)

자바의 컨커런트 API에서 **`Executor(실행자)`**는 동시성 프로그래밍을 위한 핵심 구성 요소 중 하나이다. 이 프레임워크는 스레드 생성과 관리를 추상화하고, 태스크 실행을 단순화하여, 개발자가 멀티스레드 애플리케이션을 더 쉽게 구현할 수 있도록 설계되어있다.

<br>

# 1. Executor 인터페이스

`Executor` 인터페이스는 가장 기본적인 실행자 인터페이스로, 단일 메소드 `execute(Runnable command)`를 정의한다. 이 메소드는 `Runnable` 객체를 받아서, 즉시 실행하거나 나중에 실행하기 위해 대기열에 넣는다. 이 인터페이스의 구현체는 이 태스크를 어떻게 처리할지(즉시 실행, 큐에 넣기, 스레드 풀 사용 등) 결정한다.

<br>

#### `execute` 메서드

- **파라미터**: `Runnable` - 실행할 태스크를 나타내는 `Runnable` 인터페이스이다.
- **반환값**: 없음 (`void`)
- **기능**: 제공된 `Runnable` 태스크를 실행한다. 이 메서드는 어떻게 태스크를 실행할지, 즉 새로운 스레드에서 실행할지, 스레드 풀에서 실행할지, 또는 호출 스레드에서 직접 실행할지 등을 결정한다.
- **사용 시 고려사항**: `execute` 메서드는 실행 중인 태스크에 대해 예외를 던지지 않는다. 또한, 태스크가 비동기적으로 실행되므로 메서드 호출이 반환된 후에 태스크가 완료되었는지 확인할 수단이 없다. 태스크 실행 중 발생하는 예외는 내부적으로 적절히 처리해야 한다.

### Executor 상속으로 구현 예제 코드

```java
import java.util.concurrent.Executor;

public class CustomExecutorExample {
    public static void main(String[] args) {
        Executor executor = new CustomExecutor();

        //Runnable 태스크 생성 및 실행
        Runnable task1 = () -> {
            System.out.println("Running task 1 in: " + Thread.currentThread().getName());
        };

        Runnable task2 = () -> {
            System.out.println("Running task 2 in: " + Thread.currentThread().getName());
        };

        //각 태스크를 Executor를 통해 실행 
        executor.execute(task1);
        executor.execute(task2);
    }

    static class CustomExecutor implements Executor {
        @Override
        public void execute(Runnable command) {
            Thread newThread = new Thread(command);
            newThread.start();  // 새 스레드에서 Runnable 태스크 실행
        }
    }
}
```

<br><br>


# 2. ExecutorService 인터페이스

`ExecutorService`는  Java의 java.util.concurrent 패키지에 있는 `Executor`를 상속하고 확장한 실행자 인터페이스이다. 이 인터페이스는 태스크 실행 뿐만 아니라, 실행자 서비스의 생명주기 관리, 태스크 상태 추적 및 결과 회수 기능을 제공한다. 그래서 일반적으로 컨커런트API는 ExecutorService인터페이스를 사용한다고 생각하면 된다. `ExecutorService`는 다음과 같은 중요한 메소드를 제공힌다.

- `submit(Callable<T> task)`: `Callable` 인터페이스를 구현한 태스크를 실행하고, 결과를 `Future<T>`로 반환한다.
- `invokeAll(Collection<? extends Callable<T>> tasks)`: 태스크 컬렉션을 실행하고, 각 태스크의 결과를 담은 `Future` 객체 리스트를 반환한다.
- `shutdown()`: 실행자 서비스를 점진적으로 종료한다. 이미 제출된 태스크는 처리하나, 새로운 태스크는 수락하지 않는다.
- `shutdownNow()`: 실행자 서비스를 즉시 종료하고, 현재 진행 중인 태스크를 중단한다.

<br>

#### 주요 기능

- **태스크 실행**: `execute(Runnable)` 외에도 `submit(Runnable)`, `submit(Callable)`, `invokeAll()`, `invokeAny()` 등의 메서드를 통해 태스크를 실행하고 결과를 받을 수 있다.
- **생명주기 관리**: `shutdown()` 메서드는 실행자 서비스를 그레이스풀하게 종료하도록 요청하며, `shutdownNow()`는 현재 진행 중인 태스크를 중단하고 실행자 서비스를 즉시 종료합니다. `isShutdown()`과 `isTerminated()` 메서드는 실행자의 상태를 확인한다.

<br>

## 2-1. ExecutorService 태스크 실행 메서드

### 1. `execute(Runnable command)`

- **설명**: 가장 기본적인 태스크 실행 메서드로, `Runnable` 인터페이스를 구현하는 태스크를 받아 실행한다. `Runnable` 태스크는 결과를 반환하지 않는다.
- **사용법**: 이 메서드는 비동기적으로 태스크를 실행하며, 태스크 실행이 완료되면 종료하며 예외 처리나 결과 반환 기능이 없다.

```java
package com.modern.java.practical.concurrentapi.executorservice;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorServiceExecuteExample {
    public static void main(String[] args) {
        //Executors.newSingleThreadExecutor() 메서드를 호출하여
        //단일 스레드를 사용하는 ExecutorService를 생성하며 이 실행자는 제출된 모든 태스크를 순서대로 하나의 스레드에서 처리
        ExecutorService executor = Executors.newSingleThreadExecutor();

        executor.execute(() -> {
            System.out.println("Running in: " + Thread.currentThread().getName());
        });
        
        //실행자 서비스를 종료
        executor.shutdown();
    }
}
```

<br>


### 2. `submit(Runnable task)`

- **설명**: `Runnable` 태스크를 실행하고, 태스크가 완료되면 `Future<?>` 객체를 반환한다. 이 `Future` 객체를 사용하여 태스크의 완료를 확인하거나, 태스크 실행 중 발생한 예외를 처리할 수 있다.
- **사용법**: 태스크 실행 후 결과를 기다리지 않고 다른 작업을 계속 수행할 수 있으며, 필요한 경우에 `Future`를 통해 태스크 상태를 조회할 수 있다.

```java
package com.modern.java.practical.concurrentapi.executorservice;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class ExecutorServiceSubmitRunnableExample {
    public static void main(String[] args) {
        //Executors.newSingleThreadExecutor() 메서드를 호출하여
        //단일 스레드를 사용하는 ExecutorService를 생성하며 이 실행자는 제출된 모든 태스크를 순서대로 하나의 스레드에서 처리
        ExecutorService executor = Executors.newSingleThreadExecutor();

        //submit(Runnable)을 사용하여 태스크를 실행하고, Future<?> 객체를 통해 태스크의 완료 상태를 확인
        //Runnable 태스크를 실행자에 제출하고 태스크의 처리 상태를 나타내는 Future<?> 객체를 반환
        Future<?> future = executor.submit(() -> {
            System.out.println("Running in: " + Thread.currentThread().getName());
        });

        //Future<?> 객체의 isDone() 메서드를 호출하여 태스크가 완료되었는지 확인함으로 실행 상태를 확인
        //isDone()이 true 를 반환할 확률은 낮다. 왜냐하면 태스크 제출 직후 즉시 확인하기 때문에 태스크가 실행 중일 가능성이 있다.
        if (future.isDone()) {
            System.out.println("Task completed");
        } else {
            System.out.println("Task is still running");
        }

        executor.shutdown();
    }
}
```

**submit(Runnable)**을 사용하여 태스크를 실행하고, **Future<?>** 객체를 통해 태스크의 완료 상태를 확인한다.

<br>


### 3. `submit(Callable<T> task)`

- **설명**: `Callable` 인터페이스를 구현하는 태스크를 실행하고, 실행 결과를 반환할 수 있는 `Future<T>` 객체를 반환한다. `Callable`은 `Runnable`과 달리 결과를 반환할 수 있으며 예외를 던질 수 있다.
- **사용법**: 반환된 `Future` 객체를 통해 태스크의 결과를 얻거나, 태스크가 완료될 때까지 대기하거나, 태스크 실행 중 발생한 예외를 처리할 수 있다.

```java
package com.modern.java.practical.concurrentapi.executorservice;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class ExecutorServiceSubmitCallableExample {
    public static void main(String[] args) throws Exception {
        //Executors.newSingleThreadExecutor() 메서드를 호출하여
        //단일 스레드를 사용하는 ExecutorService를 생성하며 이 실행자는 제출된 모든 태스크를 순서대로 하나의 스레드에서 처리
        ExecutorService executor = Executors.newSingleThreadExecutor();

        //Callable 작업을 submit 메서드에 제출한다.
        //Callable 은 현재 스레드의 이름과 함께 문자열(타입)을 반환한다.
        //submit 호출은 즉시 Future 객체를 반환하며, 이 객체를 통해 나중에 작업의 결과를 검색할 수 있습니다.
        Future<String> future = executor.submit(() -> {
            return "Result from " + Thread.currentThread().getName();
        });

        //future.get() 메서드를 호출하여 Callable 작업의 결과
        String result = future.get();
        System.out.println(result); //Result from pool-1-thread-1

        executor.shutdown();
    }
}
```

**submit(Callable<T>)**을 사용하여 Callable 태스크를 제출하고, 반환된 **Future<T>** 객체로부터 결과를 동기적으로 받아온다.

<br>


### 4. `invokeAll(Collection<? extends Callable<T>> tasks)`

- **설명**: `Callable` 태스크의 컬렉션을 받아 동시에 실행하고, 모든 태스크가 완료되면 각 태스크의 결과를 담은 `List<Future<T>>`를 반환합니다. 모든 태스크가 완료될 때까지 이 메서드는 블로킹된다.
- **사용법**: 일괄 처리 작업을 수행하고 각 태스크의 결과를 한 번에 처리해야 할 때 유용하다. 예를 들어, 다수의 네트워크 호출이나 계산 작업을 동시에 실행하고 결과를 수집할 때 사용할 수 있다.

```java
package com.modern.java.practical.concurrentapi.executorservice;

import java.util.Arrays;
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class ExecutorServiceInvokeAllExample {
    public static void main(String[] args) throws Exception {
        //Executors.newSingleThreadExecutor() 메서드를 호출하여
        //단일 스레드를 사용하는 ExecutorService를 생성하며 이 실행자는 제출된 모든 태스크를 순서대로 하나의 스레드에서 처리
        ExecutorService executor = Executors.newFixedThreadPool(3);

        List<Callable<String>> tasks = Arrays.asList(
                () -> "Task 1 completed",
                () -> "Task 2 completed",
                () -> "Task 3 completed"
        );

        //invokeAll() 메서드는 제공된 모든 Callable 객체들을 실행하고,
        //각 작업의 결과를 나타내는 Future 객체의 리스트를 반환하고 이 메서드는 모든 작업이 완료될 때까지 블로킹된다.
        List<Future<String>> futures = executor.invokeAll(tasks);

        for (Future<String> future : futures) {
            System.out.println(future.get());  // Blocking call
        }

        executor.shutdown();
    }
}
```

invokeAll은 주어진 모든 Callable 태스크를 실행하고, 각 태스크의 결과를 담은 **List<Future<T>>**를 반환한다.

<br>


### 5. `invokeAny(Collection<? extends Callable<T>> tasks)`

- **설명**: `Callable` 태스크의 컬렉션을 받아 실행하지만, `invokeAll`과 달리 첫 번째로 완료되는 태스크의 결과를 반환하고 나머지 태스크는 취소합니다. 이 메서드는 첫 번째 결과가 나올 때까지 블로킹됩니다.
- **사용법**: 여러 태스크 중 하나의 결과만 필요하고, 가장 빠르게 결과를 제공하는 태스크를 사용하려는 경우에 적합합니다. 예를 들어, 동일한 요청을 여러 서버에 보내고 가장 빠른 응답만 필요한 경우 사용할 수 있습니다.

```java
package com.modern.java.practical.concurrentapi.executorservice;

import java.util.Arrays;
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeoutException;

public class ExecutorServiceInvokeAnyExample {
    public static void main(String[] args) throws Exception {
        //Executors.newSingleThreadExecutor() 메서드를 호출하여
        //단일 스레드를 사용하는 ExecutorService를 생성하며 이 실행자는 제출된 모든 태스크를 순서대로 하나의 스레드에서 처리
        ExecutorService executor = Executors.newFixedThreadPool(3);

        //세 개의 Callable 작업을 정의하고 리스트로 관리하며
        //각 작업은 다른 시간(2000ms, 1000ms, 500ms) 동안 일시 중단된 후 문자열을 반환
        List<Callable<String>> tasks = Arrays.asList(
                () -> {
                    Thread.sleep(2000);
                    return "Task 1 completed";
                },
                () -> {
                    Thread.sleep(1000);
                    return "Task 2 completed";
                },
                () -> {
                    Thread.sleep(500);
                    return "Task 3 completed";
                }
        );

        //invokeAny 메서드를 호출하여 제출된 작업들 중 하나가 완료될 때까지 기다리고 가장 먼저 완료된 작업의 결과를 반환받아 출력
        String result = executor.invokeAny(tasks);
        System.out.println("First completed task result: " + result);   //First completed task result: Task 3 completed

        executor.shutdown();
    }
}
```

invokeAny 메서드를 통해 복수의 작업 중 가장 빠르게 완료되는 작업을 효율적으로 처리하는 방법을 보여주기에 특히 여러 대안 중 하나만 필요할 때 유용하다.

<br><br>

## 2-2. ExecutorService 생명주기 관리 메서드

### 1. `shutdown()`

- **설명**: `shutdown()` 메서드는 실행자 서비스를 그레이스풀하게 종료하도록 요청한다. 이 메서드를 호출하면, 더 이상 새로운 태스크를 실행자 서비스에 제출할 수 없으며, 이미 제출된 태스크는 완료될 때까지 계속 실행된다.
- **사용법**: 일반적으로 애플리케이션이 종료될 때나 더 이상 실행자 서비스가 필요하지 않을 때 호출된다. 실행자 서비스가 모든 작업을 완료하고 자원을 해제할 수 있도록 한다.

<br>


### 2. `shutdownNow()`

- **설명**: `shutdownNow()` 메서드는 실행자 서비스를 즉시 종료하려고 시도한다. 이 메서드는 현재 진행 중인 모든 태스크를 중단하고, 대기 중인 태스크 목록을 반환한다.
- **사용법**: 긴급하게 실행자 서비스를 중단해야 할 때 사용된다. 예를 들어, 사용자가 애플리케이션을 갑자기 종료하려고 할 때 유용하다. 하지만 이미 실행 중인 태스크가 중단되기 때문에 일부 작업이 완료되지 않을 수 있다.

<br>


### 3. `isShutdown()`

- **설명**: `isShutdown()` 메서드는 실행자 서비스가 종료 절차(shutdown)를 시작했는지 여부를 확인한다. `shutdown()` 또는 `shutdownNow()` 메서드가 호출되면 `true`를 반환한다.
- **사용법**: 실행자 서비스의 상태를 체크할 때 사용한다. 예를 들어, 다른 부분의 코드에서 실행자 서비스에 태스크를 제출하기 전에 이 메서드를 확인하여, 서비스가 종료 절차를 시작했는지 확인할 수 있다.

<br>


### 4. `isTerminated()`

- **설명**: `isTerminated()` 메서드는 모든 태스크가 완료되고 실행자 서비스가 완전히 종료되었는지를 확인한다. 실행자가 종료 절차를 완료했을 때 `true`를 반환한다.
- **사용법**: 주로 종료 후 처리를 하기 전에 실행자 서비스가 완전히 종료되었는지 확인하는 데 사용된다. `shutdown()` 호출 후, 모든 태스크가 완료되었는지 확인할 때 유용하다.

<br>


#### 5. `awaitTermination(long timeout, TimeUnit unit)`

- **설명**: `awaitTermination()` 메서드는 호출 스레드를 블록하고, 지정된 시간 동안 또는 모든 태스크가 완료될 때까지 대기한다. 모든 태스크가 완료되거나 타임아웃이 발생하거나 스레드가 중단될 때까지 대기한다.
- **사용법**: 이 메서드는 `shutdown()` 호출 후에 사용되며, 실행자 서비스가 완전히 종료될 때까지 현재 스레드를 대기한다. 예를 들어, 애플리케이션의 메인 스레드가 종료되기 전에 모든 배경 작업이 완료되도록 할 때 사용할 수 있다.

<br><br>


## 2-3. `ExecutorService` 객체

### `ExecutorService` 객체를 생성하는 주요 메서드와 상세 설명

`ExecutorService`는 Java의 `java.util.concurrent` 패키지에서 제공되는 인터페이스로, 멀티스레드 프로그래밍의 복잡성을 줄이고 스레드 풀을 효율적으로 관리하기 위해 사용됩니다. 이 인터페이스는 여러 구현체와 팩토리 메서드를 통해 다양한 종류의 스레드 풀을 생성합니다.


### 1. **Executors.newSingleThreadExecutor()**

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
```

- **설명**:
  - 단일 스레드로 작업을 처리하는 스레드 풀을 생성한다.
  - 작업이 큐에 순서대로 추가되며, 하나의 스레드에서 작업이 순차적으로 실행된다.
  - 작업 순서를 보장해야 하는 경우 적합하다.

- **특징**:
  - 작업이 순서대로 실행되므로 스레드 간 동기화 문제가 없다.
  - 단일 스레드이므로 하나의 작업이 오래 걸리면 전체 작업이 지연될 수 있다.

- **언제 사용해야 하는가?**
  - `작업이 순차적으로 실행되어야 하거나`, 공유 리소스에 접근할 때 스레드 안전성을 보장하고 싶을 때

- **유용한 경우**:
  - `작업 순서가 중요한 경우(예: 로깅, 데이터 처리)`
  - 단일 스레드 환경을 유지하면서 작업의 실행을 관리하고 싶을 때

- **단점**:
  - 단일 스레드이므로 작업이 느리면 전체 작업이 지연될 수 있음
  - 스레드가 비정상적으로 종료되면 실행 중인 작업이 중단될 수 있음

<br>


### 2. **Executors.newFixedThreadPool(int nThreads)**

```java
ExecutorService executor = Executors.newFixedThreadPool(4);
```

- **설명**:
  - 고정 크기의 스레드 풀을 생성한다.
  - 지정된 크기(`nThreads`)의 스레드가 생성되고, 이 크기를 초과하는 작업은 내부 큐에 저장된다.
  - CPU가 많은 작업(CPU-bound task)에 적합하다.

- **특징**:
  - 모든 스레드가 작업을 완료한 후에도 풀은 종료되지 않는다. 명시적으로 `shutdown()` 또는 `shutdownNow()`를 호출해야 한다.
  - 스레드의 개수를 초과하는 작업은 대기열(큐)에 쌓이게 된다.

- **언제 사용해야 하는가?**
  - 작업의 수가 많고, 고정된 스레드 수를 유지하면서 효율적으로 병렬 처리하고자 할 때
  - CPU-집약적인 작업에서 사용하면 효과적

- **유용한 경우**:
  - 다수의 독립적인 작업을 병렬로 실행하지만, 시스템 리소스를 초과하지 않도록 스레드 수를 제한하고 싶을 때
  - 서버와 같은 환경에서 고정된 워커 스레드 풀을 유지하고 싶을 때

- **단점**:
  - 작업이 너무 많으면 대기열(큐)에 작업이 쌓여 처리 속도가 느려질 수 있음
  - 고정된 스레드 수로 인해 작업량이 스레드 수를 초과하면 작업 지연이 발생
  - 큐가 무한정 커질 수 있어 메모리 초과(OOM) 위험이 있음

<br>


### 3. **Executors.newCachedThreadPool()**

```java
ExecutorService executor = Executors.newCachedThreadPool();
```

- **설명**:
  - 필요에 따라 스레드를 동적으로 생성하거나 재사용한다.
  - 기존에 실행 완료된 스레드가 사용 가능하면 이를 재활용하며, 그렇지 않으면 새로운 스레드를 생성한다.
  - I/O 작업(예: 네트워크 요청)과 같이 작업량이 많고 각 작업이 짧은 경우에 적합하다.

- **특징**:
  - 작업이 없으면 유지되는 스레드는 없다.
  - 무제한 크기의 스레드 풀처럼 작동할 수 있으므로 과도한 작업이 시스템 리소스를 초과할 위험이 있다.

- **언제 사용해야 하는가?**
  - 작업이 순차적으로 실행되어야 하거나, 공유 리소스에 접근할 때 스레드 안전성을 보장하고 싶을 때

- **유용한 경우**:
  - 작업 순서가 중요한 경우(예: 로깅, 데이터 처리)
  - 단일 스레드 환경을 유지하면서 작업의 실행을 관리하고 싶을 때

- **단점**:
  - 단일 스레드이므로 작업이 느리면 전체 작업이 지연될 수 있음
  - 스레드가 비정상적으로 종료되면 실행 중인 작업이 중단될 수 있음

<br>

---

### 요약 비교

| 메서드                           | 사용 시점                                     | 유용한 경우                         | 단점                                    |
|----------------------------------|---------------------------------------------|-------------------------------------|-----------------------------------------|
| `newSingleThreadExecutor`        | 작업 순서가 중요할 때                        | 순차적 작업 처리                    | 작업 지연 시 전체 작업 지연              |
| `newFixedThreadPool`             | 고정된 스레드 수 필요                        | CPU-집약적 작업                     | 대기열이 과도하게 쌓이면 처리 지연       |
| `newCachedThreadPool`            | 작업 수가 동적으로 변할 때                   | 짧은 수명 I/O 작업                  | 스레드 과도 생성 위험                    |

<br><br>


# 3. ScheduledExecutorService 인터페이스

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


## 3-1. ScheduledExecutorService 메서드

### 1. `schedule(Runnable command, long delay, TimeUnit unit)`

- **설명**: 지정된 지연 시간 후에 `Runnable` 태스크를 한 번 실행한다. `Runnable`은 결과를 반환하지 않는다.
- **사용법**: 이 메서드는 주어진 지연(`delay`)이 경과한 후 태스크를 비동기적으로 실행한다. 지연 시간은 `TimeUnit` 단위로 설정된다.

```java
package com.modern.java.practical.concurrentapi.scheduledexecutorservice;

import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ScheduledExecutorServiceScheduleExample {
    public static void main(String[] args) {
        // ScheduledExecutorService 인스턴스 생성
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

        // Runnable 태스크 정의
        Runnable task = new Runnable() {
            public void run() {
                System.out.println("Task 5초 뒤에 실행");
            }
        };

        // 태스크를 5초 후에 실행하도록 스케줄
        scheduler.schedule(task, 5, TimeUnit.SECONDS);

        // 종료를 위해 예제를 간단하게 유지
        scheduler.shutdown();
    }
}
```

<br>

### 2. `schedule(Callable<V> callable, long delay, TimeUnit unit)`

- **설명**: 지정된 지연 시간 후에 `Callable<V>` 태스크를 한 번 실행하고 결과를 반환한다.
- **사용법**: `Callable` 태스크는 실행 후 결과값을 반환한다. 이 메서드는 태스크를 주어진 지연 시간(`delay`) 후에 실행하며, 지연 시간은 `TimeUnit`으로 지정된다.

```java
package com.modern.java.practical.concurrentapi.scheduledexecutorservice;

import java.util.concurrent.*;

public class ScheduledExecutorServiceScheduleCallableExample {
    public static void main(String[] args) {
        //ScheduledExecutorService 인스턴스 생성
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

        //Callable 태스크 정의, 실행하고자 하는 태스크를 Callable<String> 인터페이스를 구현하는 클래스로 정의
        Callable<String> task = new Callable<String>() {
            public String call() throws Exception {
                return "Task 3초 뒤에 실행";
            }
        };

        //태스크를 3초 후에 실행하도록 스케줄
        Future<String> result = scheduler.schedule(task, 3, TimeUnit.SECONDS);

        try {
            //Future.get() 메서드를 호출하여 태스크의 실행 결과를 받아서 출력
            System.out.println("Result: " + result.get());
        } catch (Exception e) {
            System.err.println("Error occurred while executing the task.");
            e.printStackTrace();
        }

        //스케줄러 종료
        scheduler.shutdown();
    }
}
```

<br>

### 3. `scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`

- **설명**: 초기 지연 시간 이후에 시작해서 지정된 주기마다 `Runnable` 태스크를 반복 실행한다.
- **사용법**: 이 메서드는 태스크를 `initialDelay` 후에 처음 실행하고, 이후 주기적(`period`)으로 실행을 계속한다. 주기는 태스크의 시작 시점부터 측정된다.

```java
package com.modern.java.practical.concurrentapi.scheduledexecutorservice;

import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ScheduledExecutorServiceFixedRateScheduledExample {
    public static void main(String[] args) {
        // ScheduledExecutorService 인스턴스 생성
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

        // Runnable 태스크 정의
        Runnable task = new Runnable() {
            public void run() {
                System.out.println("태스크를 초기 지연 1초 후에 시작하고, 이후 매 2초마다 반복 실행");
            }
        };

        // 태스크를 초기 지연 1초 후에 시작하고, 이후 매 2초마다 반복 실행
        scheduler.scheduleAtFixedRate(task, 1, 2, TimeUnit.SECONDS);

        // 10초 후 스케줄러 종료 (실행 중인 태스크 완료 후)
        try {
            Thread.sleep(10000); // 10초 대기
        } catch (InterruptedException e) {
            System.err.println("Main thread interrupted.");
        }
        scheduler.shutdownNow();
    }
}
```

<br>

### 4. `scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit)`

- **설명**: 초기 지연 시간 이후에 시작해서, 각 실행 완료 후 지정된 지연 시간을 두고 `Runnable` 태스크를 반복 실행한다.
- **사용법**: 이 메서드는 각 태스크 실행이 완료된 후 지정된 지연(`delay`)을 기다린 후 다음 태스크를 실행한다. 지연은 실행 완료 후에 측정된다.

```java
package com.modern.java.practical.concurrentapi.scheduledexecutorservice;

import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ScheduledExecutorServiceScheduleWithFixedDelayExample {
    public static void main(String[] args) {
        // ScheduledExecutorService 인스턴스 생성
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);

        // Runnable 태스크 정의
        Runnable task = new Runnable() {
            public void run() {
                System.out.println("해당 Task 가 3초 동안 실행하고 완료된 다음 2초 뒤에 실행");
                try {
                    Thread.sleep(3000); // 이 태스크의 실행 시간을 3초로 설쩡
                } catch (InterruptedException e) {
                    System.err.println("Task interrupted.");
                }
            }
        };

        // 태스크를 초기 지연 1초 후에 시작하고, 각 실행 완료 후 2초의 지연으로 반복 실행
        scheduler.scheduleWithFixedDelay(task, 1, 2, TimeUnit.SECONDS);

        // 20초 후 스케줄러 종료 (실행 중인 태스크 완료 후)
        try {
            Thread.sleep(20000); // 20초 대기
        } catch (InterruptedException e) {
            System.err.println("Main thread interrupted.");
        }
        scheduler.shutdownNow();
    }
}
```

<br>


## 3-2. `ScheduledExecutorService` 객체

### `ScheduledExecutorService` 객체를 생성하는 주요 메서드와 상세 설명

### 1. **newSingleThreadScheduledExecutor()**

```java
ScheduledExecutorService executor = Executors.newSingleThreadScheduledExecutor();
```

- **설명**:
  - 단일 스레드를 이용하여 스케줄된 작업을 실행하는 `ScheduledExecutorService`를 생성한다.

- **특징**:
  - 모든 작업을 단일 스레드로 순차적으로 처리한다.
  - 작업 간 동시성 문제가 발생하지 않는다.

- **언제 사용해야 하는가?**:
  - 작업 실행 순서가 중요하거나, 작업 간 동시성 관리가 필요 없을 때 사용한다.

- **유용한 경우**:
  - 정기적인 유지보수 작업, 로그 파일 체크, 데이터베이스 백업 등 순차적 실행이 필요한 작업에 유용하다.

- **단점**:
  - 모든 작업이 단일 스레드에서 실행되기 때문에, 한 작업이 지연될 경우 전체 작업 일정에 영향을 줄 수 있다.
  - 병렬 처리가 불가능하여 작업 처리 효율이 낮을 수 있다.
  
<br>


### 2. **newScheduledThreadPool(int corePoolSize)**

```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(5);
```

- **설명**:
  - 지정된 수의 스레드를 갖는 스레드 풀을 이용하여 작업을 스케줄링하는 `ScheduledExecutorService`를 생성한다.

- **특징**:
  - 복수의 스레드에서 동시에 작업을 처리할 수 있다.
  - 높은 병렬 처리 능력을 제공한다.

- **언제 사용해야 하는가?**:
  - 여러 작업을 동시에 빠르게 처리해야 할 때, 특히 대용량 데이터 처리나 여러 클라이언트의 요청을 동시에 처리해야 하는 서버 환경에서 적합하다.

- **유용한 경우**:
  - 웹 서비스 폴링, 동시 데이터 수집, 대규모 계산 작업 등 동시에 여러 작업을 실행해야 할 때 유용하다.

- **단점**:
  - 스레드 풀 관리가 복잡할 수 있으며, 스레드 간의 동기화 및 동시성 관리가 필요하다.
  - 사용하지 않는 스레드 자원이 낭비될 수 있다.

<br>


### 3. **newSingleThreadScheduledExecutor(ThreadFactory threadFactory)**

```java
ThreadFactory customThreadFactory = new ThreadFactory() {
    @Override
    public Thread newThread(Runnable r) {
        Thread thread = new Thread(r);
        thread.setName("CustomThread");
        return thread;
    }
};

ScheduledExecutorService executor = Executors.newSingleThreadScheduledExecutor(customThreadFactory);
```

- **설명**:
  - 사용자가 정의한 `ThreadFactory`를 이용하여 단일 스레드 스케줄러를 생성한다.

- **특징**:
  - 스레드의 세부적인 설정(이름, 우선 순위 등)을 사용자가 직접 조정할 수 있다.

- **언제 사용해야 하는가?**:
  - 스레드의 세부 속성을 제어할 필요가 있는 경우, 특히 개발 및 디버깅 시 스레드를 쉽게 식별할 필요가 있을 때 사용한다.

- **유용한 경우**:
  - 스레드 모니터링이 중요한 응용 프로그램, 고성능 요구 작업, 특정 스레드에 고 우선순위 부여가 필요한 작업에 유용하다.

- **단점**:
  - 단일 스레드만 사용하기 때문에 작업 처리량이 제한된다.
  - 스레드 설정을 잘못 구성할 경우 시스템 성능에 부정적인 영향을 미칠 수 있다.

<br>


### 4. **newScheduledThreadPool(int corePoolSize, ThreadFactory threadFactory)**

```java
ThreadFactory customThreadFactory = new ThreadFactory() {
    @Override
    public Thread newThread(Runnable r) {
        Thread thread = new Thread(r);
        thread.setName("CustomThreadPoolThread-" + thread.getId());
        return thread;
    }
};

int corePoolSize = 5; // 스레드 풀의 크기 설정
ScheduledExecutorService executor = Executors.newScheduledThreadPool(corePoolSize, customThreadFactory);
```

- **설명**:
  - 사용자 정의 `ThreadFactory`와 지정된 스레드 수를 사용하여 스레드 풀 기반의 스케줄러를 생성한다.

- **특징**:
  - 스레드의 수와 각 스레드의 세부 설정을 사용자가 직접 제어할 수 있다.

- **언제 사용해야 하는가?**:
  - 복잡한 멀티태스킹과 높은 성능이 요구되는 애플리케이션에서 사용한다.

- **유용한 경우**:
  - 대규모 데이터 처리, 고성능 컴퓨팅, 병렬 데이터 처리가 필요한 과학적 계산에 유용하다.

- **단점**:
  - 스레드 풀과 스레드의 세부 설정을 관리하는 것이 복잡할 수 있으며, 오류 관리가 중요하다.
  - 자원 관리가 부적절하게 이루어질 경우 시스템 전체 성능 저하로 이어질 수 있다.

---

<br>

### 요약 비교 표

| 메서드                           | 특징                                       | 유용한 경우                       | 단점                                   |
|----------------------------------|-------------------------------------------|-----------------------------------|---------------------------------------|
| `newScheduledThreadPool`         | 지연 및 주기적 작업 스케줄링               | 주기적/타이머 기반 작업             | 작업이 길어지면 주기 유지 어려움       |
| `newSingleThreadScheduledExecutor`| 단일 스레드로 주기적 작업                  | 순차적 반복 작업                   | 작업 지연 시 전체 스케줄 지연          |


<br><br>