---
layout: post
title:  "[Practical-Java] 11. Parallel Programming-Future : Future Interface, CompletableFuture Class"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Fork/Join 프레임워크에서 RecursiveTask와 RecursiveAction의 명세서를 살펴보면 두 클래스 모두 Future 인터페이스를 구현한 추상 클래스이다.

<br>


# 병렬 프로그래밍-Future : Future 인터페이스, CompletableFuture 클래스

`Future 인터페이스`를 이용해서 비동기 연산을 실행하면 저수준의 스레드 프로그래밍을 하지 않아도 비동기 처리가 가능하다. Future 인터페이스는 연산 작업의 결과를 확인하는 용도에 그친다. 그래서 더 많은 연산 작업을 위해 자바 8에서 `CompletableFuture 클래스`를 Future인터페이스의 구현체로 추가 되었다.


# 1. Future 인터페이스

`Future` 인터페이스는 자바에서 비동기 계산의 결과를 보여준다. 이 인터페이스는 계산이 완료될 때까지 기다리거나, 계산이 완료되었는지 확인하거나, 계산을 취소하는 등의 작업을 처리한다. `Future`는 멀티스레딩 프로그램에서 매우 유용하게 사용되며, 복잡한 동시성 관리 없이 비동기 실행 결과를 처리할 수 있게 도와준다.

<br>

## 1-1. 주요 특징

1. **결과 보류 (Result Holding)**:
   - `Future`는 비동기 작업이 완료되기를 기다리는 동안 결과를 보류
   - 작업이 완료되면, `get()` 메서드를 통해 결과 확인

2. **블로킹 작업 지원**:
   - `get()` 메서드를 호출하면 해당 호출이 작업의 결과를 받을 때까지 호출 스레드를 블로킹
   - 결과가 준비될 때까지 다른 작업을 하지 못하게 하는 단점이 있지만, 필요한 경우 결과를 기다리는 간단한 방법을 제공

3. **타임아웃 설정 가능**:
   - `get(long timeout, TimeUnit unit)` 메서드를 사용하면 지정된 시간 동안만 결과를 기다릴 수 있음
   - 이 시간이 지나면 `TimeoutException`이 발생하여 블로킹 상태에서 종료

4. **작업 취소 지원**:
   - `cancel(boolean mayInterruptIfRunning)` 메서드를 사용하면 진행 중인 작업을 취소
   - `mayInterruptIfRunning` 파라미터가 `true`로 설정되면, 실행 중인 스레드에 대해 인터럽트를 시도하여 작업을 중단시킬 수 있음

5. **작업 완료 확인**:
   - `isDone()` 메서드를 통해 작업이 완료되었는지 여부를 확인할 수 있음
   - 해당 메서드는 작업이 정상적으로 완료되었거나, 예외가 발생했거나, 취소되었을 때 `true`를 반환

6. **작업 취소 확인**:
   - `isCancelled()` 메서드를 통해 작업이 취소되었는지 여부를 확인할 수 있음
   - 작업이 취소에 성공하면 `true`를 반환

<br>


## 1-2. 주요 메소드

- **`boolean cancel(boolean mayInterruptIfRunning)`**:
   - 이 메소드는 아직 완료되지 않은 작업을 취소하려고 시도
   - `mayInterruptIfRunning` 파라미터가 `true`인 경우, 진행 중인 스레드를 중단시키려 시도
   - 작업이 성공적으로 취소되면 `true`를 반환, 그렇지 않으면 `false`를 반환

- **`boolean isCancelled()`**:
   - 작업이 취소되었는지 여부를 반환
   - 작업이 취소되면 `true`를 반환

- **`boolean isDone()`**:
   - 작업이 완료되었는지 여부
   - 작업이 정상적으로 완료/예외발생/취소 되었을 때 `true`를 반환

- **`V get()`**:
   - 비동기 작업의 결과를 가져옴
   - 이 메소드는 결과가 준비될 때까지 현재 스레드를 블로킹
   -  작업이 완료되면 결과를 반환, 만약 작업 중 예외가 발생한 경우 `ExecutionException`을 던짐

- **`V get(long timeout, TimeUnit unit)`**:
   - 지정된 시간 동안 결과를 기다린 후 결과를 반환
   - 지정된 시간이 지나면 `TimeoutException`을 던짐
   - 이 메소드도 현재 스레드를 블로킹

<br>

### Future 예제 코드-1

다음은 `ExecutorService`를 사용하여 비동기 작업을 제출하고 `Future`를 통해 결과를 처리하는 간단한 예제로 Future 인터페이스를 사용하면 비동기 작업의 결과를 쉽게 처리할 수 있지만, 결과를 기다리는 동안 현재 스레드가 블로킹된다는 단점이 있다.

```java
public class FutureSimpleExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executors.newSingleThreadExecutor();

        Future<String> future = executor.submit(() -> {
            System.out.println("비동기 작업 실행");
            TimeUnit.SECONDS.sleep(3);
            return "비동기 3초 작업 완료";
        });

        System.out.println("메인 스레드 다른 작업 수행");

        //결과가 필요할 때까지 대기
        String result = future.get(); // 블로킹 호출
        System.out.println("결과: " + result);

        executor.shutdown();
    }
}
```

```
메인 스레드 다른 작업 수행
비동기 작업 실행
결과: 비동기 3초 작업 완료
```

위 예제에서는 `submit` 메소드를 사용하여 비동기적으로 작업을 실행하고, `get` 메소드를 사용하여 그 결과를 블록킹 되어 기다린다. 메인 스레드는 결과가 준비될 때까지 다른 작업을 수행된다.

아래는 또 다른 Future 인터페이스 에제코드이다.

<br>

### Future 예제 코드-2

```java
public class FutureMultiWaitExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3);

        //비동기 작업 제출
        Future<Integer> future1 = executor.submit(() -> {
            TimeUnit.SECONDS.sleep(1);
            return 10;
        });

        Future<Integer> future2 = executor.submit(() -> {
            TimeUnit.SECONDS.sleep(2);
            return 20;
        });

        Future<Integer> future3 = executor.submit(() -> {
            TimeUnit.SECONDS.sleep(3);
            return 30;
        });

        try {
            //모든 작업의 결과를 기다림
            Integer result1 = future1.get();
            Integer result2 = future2.get();
            Integer result3 = future3.get();

            System.out.println("작업 1 결과: " + result1);
            System.out.println("작업 2 결과: " + result2);
            System.out.println("작업 3 결과: " + result3);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        } finally {
            executor.shutdown();
        }
    }
}
```

```
작업 1 결과: 10
작업 2 결과: 20
작업 3 결과: 30
```

세 개의 비동기 작업을 ExecutorService의 submit 메소드를 사용하여 제출합니다. 각 작업은 처리하고 future.get() 메소드는 각 작업의 결과가 준비될 때까지 현재 스레드를 블로킹하며, 모든 작업이 완료되면 결과를 출력한다. Future를 활용하면 여러 비동기 작업의 결과를 효율적으로 관리할 수 있으며, 각 작업의 완료 시간에 맞춰 결과를 순차적으로 처리할 수 있다.

이처럼 `Future` 인터페이스를 사용하면 비동기 작업의 결과를 쉽게 처리할 수 있지만, 결과를 기다리는 동안 현재 스레드가 블로킹된다는 단점이 있다. 이러한 단점을 해결하기 위해 `CompletableFuture`와 같은 더 고급 기능을 제공하는 클래스가 등장했다.

<br><br>


# 2. CompletableFuture 클래스

`CompletableFuture` 클래스는 `자바에서 비동기 프로그래밍을 보다 효과적으로 수행할 수 있도록 설계된 클래스`로, `Future` 인터페이스의 단점을 보완하기에 추가 되었다. `java.util.concurrent` 패키지에 속해 있으며, 비동기 계산의 결과를 표현하고, 여러 비동기 작업을 효율적으로 조합하고, 비동기 작업에 대한 결과 처리 및 에러 처리를 유연하게 처리할 수 있는 기능을 제공한다.

<br>

## 2-1. 주요 특징

1. **비동기 작업의 조합 및 체이닝**:
   - `CompletableFuture`는 여러 개의 비동기 작업을 순차적, 병렬적으로 조합하고, 작업의 결과를 체이닝 방식으로 다룰 수 있음
   - `thenCompose`, `thenCombine` 같은 메서드를 사용하여 여러 단계의 비동기 로직을 하나로 결합 가능

2. **비동기 작업의 트리거**:
   - `runAsync`와 `supplyAsync` 메서드를 사용해 비동기 작업을 쉽게 시작할 수 있음
   - 이들은 각각 `Runnable`과 `Supplier` 인터페이스를 매개변수로 받아 비동기 실행을 트리거

3. **결과 처리 및 소비**:
   - `thenApply`, `thenAccept`, `thenRun` 같은 메서드를 통해 비동기 작업의 결과를 변환, 소비 또는 추가 작업 실행 등의 연속적인 작업을 할 수 있음
   - 이 메서드들은 비동기 작업의 결과가 준비되면 실행됨

4. **에러 처리**:
   - `exceptionally`, `handle`, `whenComplete` 등의 메서드를 통해 비동기 작업 중 발생할 수 있는 에러를 적절히 처리할 수 있음
   - 이를 통해 로직을 강건하게 유지하고, 예외 상황에서도 적절한 대응 가능

5. **블로킹 없는 비동기 프로그래밍**:
   - `CompletableFuture`는 `get()` 메서드를 사용해 결과를 가져오는 일반적인 `Future`와 달리, 결과를 기다리는 동안 다른 작업을 할 수 있도록 도움
   - 결과가 준비되면 콜백이 트리거되어 처리됨.

6. **스레드 커스터마이징**:
   - 비동기 작업을 실행할 스레드를 커스터마이즈할 수 있도록, `Executor`를 사용하여 비동기 메서드를 실행할 수 있다. 이를 통해 리소스 사용을 최적화하고 성능을 향상시킬 수 있음

<br>


## 2-2. 주요 메서드

### 작업 생성

- **`supplyAsync(Supplier<U> supplier)`**:
  - 비동기적으로 값을 공급하고 `CompletableFuture<U>`를 반환
- **`runAsync(Runnable runnable)`**:
  - 반환값 없이 비동기적으로 작업을 실행하고 `CompletableFuture<Void>`를 반환
- **`runAsync(Runnable runnable, Executor executor)`**:
  - 사용자 정의 실행자를 사용하여 반환값 없이 비동기 작업을 실행

### 결과 처리 (Transformation)

- **`thenApply(Function<? super T,? extends U> fn)`**:
  - 이전 단계의 결과를 받아 함수를 적용하고, 결과로 새로운 `CompletableFuture<U>`를 반환
- **`thenApplyAsync(Function<? super T,? extends U> fn)`**:
  - 비동기적으로 `thenApply`를 수행
- **`thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)`**:
  - 사용자 정의 실행자를 사용하여 비동기적으로 `thenApply`를 수행

### 결과 소비 (Consumption)

- **`thenAccept(Consumer<? super T> action)`**:
  - 이전 단계의 결과를 소비하는 액션을 실행
- **`thenAcceptAsync(Consumer<? super T> action)`**:
  - 비동기적으로 `thenAccept`를 수행
- **`thenAcceptAsync(Consumer<? super T> action, Executor executor)`**:
  - 사용자 정의 실행자를 사용하여 비동기적으로 `thenAccept`를 수행

### 작업 연결 (Combination)

- **`thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)`**:
  - 두 `CompletableFuture`의 결과를 조합
- **`thenCombineAsync(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)`**:
  - 비동기적으로 두 `CompletableFuture`의 결과를 조합
- **`thenCompose(Function<? super T, ? extends CompletionStage<U>> fn)`**:
  - 이전 단계의 결과를 받아 새로운 `CompletableFuture`를 생성하고 연결

### 예외 처리

- **`exceptionally(Function<Throwable, ? extends T> fn)`**:
  - 비동기 실행 중 예외가 발생했을 때 예외를 처리하고 기본 값을 제공
- **`handle(BiFunction<? super T, Throwable, ? extends U> fn)`**:
  - 이전 단계의 결과와 예외를 모두 처리할 수 있음
- **`whenComplete(BiConsumer<? super T, ? super Throwable> action)`**:
  - 성공적으로 완료되었거나 예외가 발생했을 때 실행할 작업을 지정
- **`whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action)`**:
  - 비동기적으로 `whenComplete`를 수행
- **`whenCompleteAsync(BiConsumer<? super T, ? super Throwable> action, Executor executor)`**:
  - 사용자 정의 실행자를 사용하여 비동기적으로 `whenComplete`를 수행

### 동작 실행 (Actions without Results)

- **`thenRun(Runnable action)`**:
  - 이전 단계의 결과와 상관없이 주어진 작업을 실행
- **`thenRunAsync(Runnable action)`**:
  - 비동기적으로 `thenRun`을 수행
- **`thenRunAsync(Runnable action, Executor executor)`**:
  - 사용자 정의 실행자를 사용하여 비동기적으로 `thenRun`을 수행

<br>


### CompletableFuture thenApply(결과 처리) 메서드 예제 코드-1

```java
import java.util.concurrent.*;
import java.util.function.Consumer;
import java.util.function.Function;
import java.util.function.Supplier;

public class CompletableFutureSimpleExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //비동기 작업으로 숫자 생성
        CompletableFuture<Integer> future = CompletableFuture.supplyAsync(new Supplier<Integer>() {
            @Override
            public Integer get() {
                System.out.println("비동기 작업으로 5 리턴");
                return 5;
            }
        });
        //CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
        //    System.out.println("Original value generated asynchronously.");
        //    return 5;
        //});

        //결과에 10을 더하는 작업
        CompletableFuture<Integer> resultFuture = future.thenApply(new Function<Integer, Integer>() {
            @Override
            public Integer apply(Integer value) {
                System.out.println("이전 단계의 결과 5에 10 더하기 작업");
                return value + 10;
            }
        });
        //CompletableFuture<Integer> resultFuture = future.thenApply(value -> {
        //    System.out.println("Adding 10 to the original value.");
        //    return value + 10;
        //});

        //최종 결과 출력
        resultFuture.thenAccept(new Consumer<Integer>() {
            @Override
            public void accept(Integer result) {
                System.out.println("결과 : " + result);
            }
        });

        // 메인 스레드가 종료되지 않도록 대기
        resultFuture.get(); // 이 호출은 필요한 경우 결과가 준비될 때까지 현재 스레드를 블로킹합니다.
    }
}
```

```
비동기 작업으로 5 리턴
이전 단계의 결과 5에 10 더하기 작업
결과 : 15
```

<br>


### CompletableFuture thenCombine(작업 결합) 메서드 예제 코드-2

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

public class CompletableFutureCombinationExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //첫 번째 비동기 작업: 숫자 5 생성
        CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> {
            System.out.println("첫 번째 비동기 작업 시작");

            try {
                Thread.sleep(5000);
                System.out.println("첫 번째 비동기 작업 5초 동안 처리");

            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return 5;
        });

        //두 번째 비동기 작업: 숫자 10 생성
        CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> {
            System.out.println("두 번째 비동기 작업 시작");

            try {
                Thread.sleep(3000);
                System.out.println("두 번째 비동기 작업 3초 동안 처리");
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return 10;
        });

        //두 결과를 조합하여 합산
        CompletableFuture<Integer> resultFuture = future1.thenCombine(future2, (result1, result2) -> {
            return result1 + result2;
        });

        //최종 결과 출력
        resultFuture.thenAccept(result -> System.out.println("결과를 조합하여 합산 : " + result));

        //모든 작업 완료를 기다림
        resultFuture.get();
    }
}
```

```
첫 번째 비동기 작업 시작
두 번째 비동기 작업 시작
두 번째 비동기 작업 3초 동안 처리
첫 번째 비동기 작업 5초 동안 처리
결과를 조합하여 합산 : 15
```

<br>


### CompletableFuture 대량 데이터 비동기처리 예제 코드-3

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ThreadLocalRandom;
import java.util.stream.Collectors;

public class InsuranceCalculationExample {
    public static void main(String[] args) throws InterruptedException, ExecutionException {

        List<CompletableFuture<String>> futures = new ArrayList<>();

        //100명의 고객에 대한 보험료 계산
        for (int i = 0; i < 100; i++) {
            final int customerId = i + 1;
            CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> calculateInsurance(customerId));
            futures.add(future);
        }

        //모든 계산 된 고객 보험료 futures 를 CompletableFuture 로 하나로 결합
        CompletableFuture<Void> allDone = CompletableFuture.allOf(futures.toArray(new CompletableFuture[0]));

        //모든 작업이 완료된 후 다시 List 데이터로 도출
        CompletableFuture<List<String>> allResults = allDone.thenApply(v -> {
            return futures.stream()
                    .map(CompletableFuture::join) // 결과를 추출
                    .collect(Collectors.toList());
        });

        //결과 출력
        allResults.thenAccept(results -> {
            results.forEach(result -> System.out.println("고객 ID:" + result));
        });

        //모든 작업 완료를 기다림
        allResults.get();
    }

    //고객들의 보험료 계산
    private static String calculateInsurance(int customerId) {
        //실제 보험료 계산 로직이 있어야 함)
        int insurance = ThreadLocalRandom.current().nextInt(1000, 5000);
        //System.out.println("고객 ID : " + customerId + ", 보험료 산출 : " + insurance);
        return customerId + "|보험료:" + insurance;
    }
}
```

<br><br>


---