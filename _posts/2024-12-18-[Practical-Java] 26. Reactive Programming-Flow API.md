---
layout: post
title:  "[Practical-Java] 26. Reactive Programming-Flow API"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---


많은 프로그래밍 언어들이 반응형 프로그래밍(Reactive Programming)을 지원하고 있다. 자바도 이러한 시대적 흐름에 맞춰 반응형 프로그램을 위한 기법들을 제시하고 있고 자바에서 일반적인 방법으로는 RxJava라는 라이브러리를 사용하는 것이다. 자바9에서는 라이브러리의 없이 반응형 프로그램밍을 할 수 있는 `Flow API`를 제공하고 있다. 네이밍을 보면 알 수 있듯이 반응형 프로그래밍의 기본 개념은 `흐름(FLow)`이다.

<br><br>


# 1. Flow API 개요

**Reactive Programming**은 데이터 스트림이 발생하는 이벤트(데이터)를 **비동기**, **논블로킹(Non-blocking)** 방식으로 처리하면서, 데이터의 변화(이벤트 발생)가 전파되는 과정을 효율적으로 설계하는 프로그래밍 패러다임이다.

Java에서는 이러한 Reactive Streams 사양을 지원하기 위해 Java 9부터 `java.util.concurrent.Flow` 패키지를 도입했다. 이 **Flow API**는 Reactive Streams 표준을 기반으로 다음과 같은 인터페이스를 제공한다. `Flow API`는 Reactive Streams 사양을 기반으로 하며, 데이터 스트림의 `발행(publishing)`과 `구독(subscription)`을 관리하여 개발자가 비동기 데이터 스트림 처리를 쉽게 한다.

<br>

---


# 2. 특징

1. **Back-Pressure 지원**  
   - 소비자(Subscriber)가 처리할 수 있는 양만큼만 데이터를 요청하도록 설계해, 생산자(Publisher)가 데이터를 무작정 밀어넣는 문제를 방지한다.  
   - 이를 통해 **프로듀서(생산자)**와 **컨슈머(소비자)** 간의 속도 차이로 발생할 수 있는 리소스 고갈이나 오버플로우를 예방할 수 있다.

2. **비동기(Asynchronous) 및 논블로킹(Non-blocking)**  
   - 이벤트(데이터)가 발생할 때마다 콜백 방식으로 전달해, 쓰레드를 효율적으로 활용한다.  
   - 블로킹 방식이 아니라 **이벤트-드리븐**으로 동작하기 때문에, 빈번한 IO 작업이나 대규모 동시 처리에 적합하다.

3. **컴포저블(Composable) 스트림**  
   - 여러 스트림을 연결해 조합(Pipeline 구성)하고 변환할 수 있다.  
   - 예: 필터, 맵, 리듀스 등의 연산을 체이닝해 깔끔하게 스트림 처리를 설계할 수 있다.

4. **Reactive Streams 표준 준수**  
   - Spring WebFlux, Akka Streams, Project Reactor, RxJava 등 다양한 Reactive 라이브러리와 상호 호환성이 높다.  
   - 표준화된 인터페이스를 따르므로, 프로젝트에서 라이브러리를 교체하거나 확장하기가 쉽다.

5. **효율적 자원 활용**  
   - 비동기로 처리하고, 필요 이상으로 데이터를 밀어넣지 않으므로 CPU나 메모리 사용량을 최적화할 수 있다.

<br>

---


# 3. 장점

1. **고성능, 고확장성(Scalability)**  
   - 논블로킹 방식으로, 요청-응답(데이터 스트림) 처리를 병렬로 진행할 수 있어 고성능을 달성하기 쉽다.  
   - 요청량이 많아져도 안정적으로 확장(Scale out)하기 좋다.

2. **높은 응답성(Responsiveness)**  
   - 빠른 이벤트 처리와 호출 스케줄링으로, 시스템이 즉각적으로 응답하도록 구현하기 용이하다.  
   - 특히 사용자 이벤트가 빈번하게 발생하는 환경(웹소켓 등)에서 유리하다.

3. **명확한 구조**  
   - Publisher, Subscriber, Processor, Subscription 역할이 구분되어 있어, 데이터 흐름(Flow)이 명시적이고 관리하기 쉽다.

4. **Back-Pressure 통한 안정성**  
   - 컨슈머가 처리할 수 있는 양만큼 데이터를 받아오므로, 병목현상이나 OutOfMemory 에러 등의 잠재적 위험을 크게 줄일 수 있다.

<br>

---


# 4. 인터페이스 목록

주요 인터페이스와 메서드명과 기능에 대한 설명이다.

### ◼︎ **`Flow.Publisher<T>`**  

- **구독**(`subscribe(Subscriber<? super T> subscriber)`)을 통해 Subscriber에게 데이터를 발행(Publish)한다.  
- Subscriber가 구독을 요청하면, 내부적으로 `Subscription`을 생성해 Subscriber에게 전달한다.


### ◼︎ **`Flow.Subscriber<T>`**

- Publisher가 발행하는 데이터를 **구독**(Subscribe)하고, 데이터(아이템)가 들어올 때(`onNext`) 원하는 작업을 수행한다.  
- 주요 콜백 메서드
  - `onSubscribe(Subscription subscription)`: 구독이 시작될 때 호출  
  - `onNext(T item)`: 새 아이템 수신 시 호출  
  - `onError(Throwable throwable)`: 스트림 처리 중 에러 발생 시 호출  
  - `onComplete()`: 모든 데이터 처리가 완료되었을 때 호출


### ◼︎ **`Flow.Subscription`**

- Subscriber가 Publisher에게 **얼마나 많은 데이터를 요청**할 것인지를 조절(Back-Pressure)하는 인터페이스다.  
- 주요 메서드
  - `request(long n)`: n개의 아이템을 요청한다  
  - `cancel()`: 구독을 취소한다


### ◼︎ **`Flow.Processor<T,R>`**

- Publisher이자 Subscriber인 존재로, 입력 스트림(T)을 받아서 변환(Processing)하고, 변환된 결과(R)를 다시 다음 Subscriber에게 발행한다.  
- 예: `Processor<String, Integer>`가 문자열을 정수로 변환한 뒤 발행


### ◼︎ **`SubmissionPublisher<T>`**

- **`Flow.Publisher<T>`**의 하위 구현체로, **반응형 스트림(Reactive Streams)** 표준 지원 클래스이다.  
- 내부적으로 **Executor(예: ForkJoinPool)**를 사용해 **비동기적으로 아이템을 전송**하고, `submit(item)`을 통해 쉽게 데이터를 발행(Publish)할 수 있다.  
- **Back-Pressure**(`request(n)`)를 지원해, 구독자(Subscriber)가 처리할 수 있는 범위 내에서만 데이터를 Push한다.  
- **테스트나 프로토타입** 단계에서 **간단히 Reactive Streams를 구현**해볼 때 유용하다.  

<br>

---


# 5. Flow API의 `역할 기준`으로 정리

`Publisher(발행자), Subscriber(구독자), Processor(중간처리자)로 3가지 역할`로 구분되고 Publisher와 Subscriber가 맺는 `계약(Contract: 구독 관계를 캡슐화한 메시지)을 의미`하는 `Subscription(구독)`로 데이터 흐름(Flow)이 명시적이고 관리하기 쉽다고 했는데 아래 내용은 **Reactive Streams**(또는 **Flow API**)의 흐름을, **생산자(Producer)**, **중간처리자(Processor)**, **소비자(Consumer)** 세 역할을 기준으로 구분해 정리한 것이다. 여기서 **Publisher**는 데이터를 발행하는 발행자(생산자), **Subscriber**는 데이터를 소비하는 구독자(소비자) 그리고 중간에서 데이터를 가공/변환하는 **Processor**가 중간처리자에 대응한다.


## ① `생산자(Publisher)` 역할

1. **데이터 생성**  
   - 가장 먼저, 데이터를 **직접 생성하거나**(예: 센서값, 외부 API에서 들어오는 이벤트)  
     또는 **데이터 소스와 연결**해(예: DB에서 데이터를 읽어들임) **데이터 발행**(Publish) 준비를 한다.

2. **구독 허용**  
   - Producer는 `subscribe(Subscriber<?> subscriber)`를 구현하여, 구독자가 들어올 때 **Subscription**을 제공할 수 있다.  
   - 이때, 연결된 모든 구독자가 데이터를 받을 수 있도록, 내부적으로 **Subscription** 목록을 관리하거나, 단일 구독만 허용하도록 설계하기도 한다.

3. **데이터 발행**  
   - 구독이 체결되고, 구독자가 `request(n)`을 호출하면(Back-Pressure), Producer는 요청된 만큼의 데이터를 **`onNext()`**로 전달한다.  
   - 데이터가 더 이상 없으면 `onComplete()`, 에러가 발생하면 `onError()`를 통해 알린다.

<br>


## ② `중간처리자(Processor)` 역할

1. **Publisher & Subscriber 역할**  
   - Processor는 **`Publisher`**이자 **`Subscriber`**이므로, **`상위 Producer`**`가 발행한 데이터를 구독(Subscriber)하고,`  
     `동시에` **`하위 Consumer`**`에게 다시 발행(Publisher)해 전달한다.`

2. **데이터 변환(Transform) & 필터링(Filter)**  
   - Processor는 `onNext(T item)`으로 받은 데이터를 가공/변환/필터링 후, 이를 `onNext(R item)` 형태로 **다음 단계**에 전달할 수 있다.  
   - 예: 문자열을 정수로 변환, 특정 조건에 맞는 데이터만 걸러서 전달 등.

3. **Back-Pressure 중계**  
   - Processor는 **구독자로서** 상위 Producer에게 데이터를 얼마나 받을지 `request(n)`으로 요청하고,  
   - **발행자로서** 하위 Consumer에게 동일하게 구독과 데이터를 전달한다.  
   - 이 과정을 통해 **여러 Processor**가 체인 형태로 연결돼 복잡한 파이프라인을 이룰 수 있다.

4. **에러 전달 & 완료 전달**  
   - 상위 Producer에서 `onError()`나 `onComplete()`가 호출되면, Processor는 이를 하위 Consumer에게도 전달한다.  
   - 중간 처리 중에 에러가 발생해도, 즉시 `onError()`를 통해 하위 단계에 전달하고 흐름이 종료된다.

<br>


## ③ `소비자(Subscriber)` 역할

1. **구독 시작**  
   - Consumer는 Producer(또는 Processor)의 `subscribe()` 메서드를 호출해 구독한다.  
   - 내부적으로 `onSubscribe(Subscription subscription)` 콜백을 받아, `Subscription`을 통해 **데이터 요청량**을 제어할 수 있다.

2. **데이터 소비**  
   - Producer(또는 중간 Processor)에서 `onNext(T item)` 메서드로 데이터를 전달해오면, Consumer는 해당 데이터를 **실제 사용하는 로직**을 수행한다.  
   - 예: UI 갱신, 파일 저장, 누적 계산, 로그 처리, 알림 서비스 전달 등.

3. **Back-Pressure 제어**  
   - Consumer는 자신의 처리 속도(메모리, CPU, 처리 지연 등)를 고려해, `request(n)`을 통해 **얼마나 많은 데이터를 받을 것인지**를 결정한다.  
   - 필요 시 `cancel()`을 호출해 더 이상 데이터를 받지 않고 구독을 중단할 수 있다.

4. **에러 및 완료 처리**  
   - 에러가 발생하면 `onError(Throwable t)`로 알림받고, 해당 처리(로그, 사용자 알림 등)를 수행한 뒤 구독이 종료된다.  
   - 모든 데이터가 정상적으로 전달되면 `onComplete()`가 호출되고, 구독이 정상 종료된다.

<br>


## ④ `구독(Subscription)` 계약(객체/메시지)

1. **구독 관계 설정**  
   - Producer(또는 Processor)가 Subscriber에게 `onSubscribe(Subscription subscription)`를 호출할 때 함께 전달되는 객체다.  
   - 각 **구독 관계**(예: `Publisher → Subscriber` 한 쌍)마다 **1:1**로 생성되며, 구독을 식별하고 연결을 유지하는 역할을 한다.

2. **데이터 요청 제어 (Back-Pressure)**  
   - Subscriber(또는 Processor)가 `request(long n)` 메서드를 호출하면, Subscription을 통해 **“n개 만큼 데이터를 보내달라”**고 Publisher에게 알린다.  
   - 이로써 **소비자**(Subscriber)가 **자신의 처리 속도**에 맞춰 **데이터를 수신**하게 되며, 한 번에 너무 많은 데이터를 받지 않도록 제어한다.

3. **구독 취소 (cancel)**  
   - Subscriber가 더 이상 데이터를 받고 싶지 않거나, 에러 또는 완료 처리 이후 추가 작업이 불필요할 경우 `cancel()`을 호출해 **구독을 해지**할 수 있다.  
   - 취소된 이후에는 `onNext()` 등의 데이터 발행이 더 이상 일어나지 않고, 관련 리소스가 정리된다.

4. **구독 상태 관리**  
   - Subscription 내부 구현체는 보통 **Publisher**가 소유하거나, Processor 내부에서 **상위 구독 / 하위 구독**을 각각 관리한다.  
   - 현재 요청량(얼마나 더 보낼 수 있는지), 구독 취소 여부 등의 **상태**를 가지고 있으며, `request(n)` 호출이 반복될 때마다 **남은 요청량**을 갱신한다.

5. **중개자 역할**  
   - `Subscription`은 **Publisher**와 **Subscriber** 사이에서 **데이터 발행 흐름**을 중개한다.  
   - Subscriber가 `request(n)`으로 `'Pull'` 의도를 알리면, Subscription이 Publisher가 `'Push'`를 할 수 있도록 제어해, **Push와 Pull이 결합된** 하이브리드 모델을 실현한다.

<br>


## ◼︎ 전체 흐름 요약

```
┌────────────┐   (1) subscribe(...)  ┌──────────────┐ (2) subscribe(...) ┌────────────┐
│  Producer  │ --------------------> │   Processor  │ -----------------> │ Subscriber │
│(Publisher) │                       │(Pub & Subscr)│                    │ (Consumer) │
└──────┬─────┘                       └───────┬──────┘                    └──────┬─────┘
       │                                     │                                  │
       │  onSubscribe(SubscriptionA)         │ onSubscribe(SubscriptionB)       │
       │                                     │                                  │
       │  request(n)                         │  request(m)                      │
       │  cancel()                           │  cancel()                        │
       ▼                                     ▼                                  │
┌─────────────────────┐              ┌───────────────────┐                      │
│   SubscriptionA     │ <----------> │   SubscriptionB   │ <--------------------┘
│  (Producer->Proc)   │   메시지/객체   │   (Proc->Subs)    │ (요청, 취소 등 조정)
└─────────────────────┘              └───────────────────┘ onNext,onError,onComplete
```

1. **데이터 발행(Producer/Publisher)**  
   - 말 그대로 **데이터를 생성하거나**(예: 외부 API, 센서, DB 등), **가져와서** 전달할 준비를 하는 주체이다.  
   - “구독 연결(subscribe)” 시점에 **“구독 제어권(Subscription)”**을 만들고, **중간 처리자(Processor)** 또는 **소비자(Subscriber)**에게 **`onSubscribe(...)`**로 알린다.

2. **중간 처리(Processor)**  
   - **Publisher**이면서 **Subscriber** 역할을 동시에 맡아, **상위(생산)로부터 받은 데이터를 변환, 필터링, 가공** 후, 다시 **하위(소비)로 발행**한다.  
   - 가령 *문자열 → 정수 변환*, *데이터 필터링*, *누적 계산* 등 각종 중간 로직을 삽입할 수 있으며, 여러 Processor가 체인(파이프라인) 형태로 이어질 수 있다.

3. **데이터 소비(Subscriber/Consumer)**  
   - 최종적으로 **데이터를 활용**하는 주체이다. 예를 들어, 화면에 표시, 파일 저장, 통계 처리, 알림 전송 등을 수행한다.  
   - 구독이 시작되면 `onSubscribe(...)`로 **Subscription**을 전달받고, **얼마나 많은 데이터를 받을지**(`request(n)`) 결정하거나, 필요 시 `cancel()`로 구독을 중단한다.

4. **Subscription (구독 제어권)**  
   - 한 쌍의 **구독 관계**(예: Publisher→Subscriber)마다 **1개씩** 생성되며, **Back-Pressure**(데이터 요청·취소)와 **연결 상태**를 관리한다. 
   - 간단히 말해, “**어느 구독자**가 **얼마만큼 데이터를** 수신할지”를 결정하는 **매개체**이다.
   - 구독 객체/메시지(`Subscription`)
     - Subscriber(또는 Processor) 입장에서는 이 Subscription을 보관해, 이후 request(n)·cancel() 등을 통해 데이터 흐름을 제어한다.
    - `request(n)` 호출 시 **해당 구독 관계**에서 **n개** 데이터만 추가로 발행(Push)하도록 제어한다.  
    - `cancel()` 호출 시 해당 구독이 즉시 해지되어 **더 이상 데이터가 전달되지 않는다**.
  
5. **Push & Pull 혼합 방식**  
   - 소비자(Subscriber)가 `request(n)`으로 **수용 가능한 데이터 양**을 `“Pull”`로 선언하면,  
   - 생산자(Producer)가 `onNext()` 콜백으로 **실제 데이터**를 `“Push”`해 전달한다.  
   - 이 덕분에, 소비자가 한 번에 너무 많은 데이터를 받아 과부하가 걸리는 상황을 방지할 수 있다.

<br>

위 도식화를 통해, **Producer(또는 상위 Processor)**는 필요한 만큼만 데이터를 `onNext()`로 **Push**하고, **Subscriber(또는 하위 Processor)**는 자신이 처리 가능한 양을 `request(n)`으로 **Pull**하게 된다. 이 **혼합(Push & Pull) 모델**을 매개하는 것이 바로 **`Subscription`**이며, 각 구간(Producer→Processor, Processor→Subscriber)마다 1개의 `Subscription`이 존재해 **Flow(흐름)**를 안전하고 유연하게 제어한다.

결론은 **“누가 데이터를 만들고, 중간에 어떻게 가공하며, 최종적으로 누가 소비하는가”**를 한눈에 파악할 수 있고, 
**각 구독 관계마다 Subscription**이 생겨 **데이터 요청량**이나 **구독 취소**를 조절한다는 점을 이해할 수 있다.

<br>

---


# 6. 반응형 프로그래밍 구현방법 Push & Pull

Reactive Streams(Flow API) 관점에서 보면, 단순히 **Push냐 Pull이냐**라고 딱 잘라 말하기보다는, **Push와 Pull이 결합된 형태**로 보는 것이 더 정확하다. 전통적인 동기(블로킹) 방식과 달리, Reactive Programming에서는 **`소비자(Subscriber)가 요청(request(n))`**을 통해 자신이 처리할 수 있는 아이템 개수를 명시하고, 그 요청 범위 내에서 **`생산자(Publisher)가 데이터를 Push`**해 주는 구조이기 때문이다.


## 6-1. 전통적인 Pull 모델 vs Push 모델

1. **`Pull` 기반**  
   - 보통 동기(블로킹) 호출에서, 소비자(또는 호출자)가 서버나 데이터 소스에 **직접 요청**을 보내고, 응답(Response)을 **가져온다**(Pull).  
   - 예: 데이터베이스 쿼리, REST API GET 호출 등.  
   - 소비자가 필요한 시점에 “Give me something” 식으로 요청을 해서 데이터를 얻어온다.

2. **`Push` 기반**  
   - 생산자(또는 이벤트 소스)가 **주기적으로** 또는 이벤트가 발생하는 즉시 **소비자에게 데이터를 ‘보내준다’**(Push).  
   - 예: WebSocket, SSE(Server-Sent Events), 콜백 기반 이벤트 리스너 등.  
   - 소비자는 별도의 요청을 하지 않아도, 생산자가 발생하는 데이터를 계속 받아서 처리한다.

<br>


## 6-2. Reactive Streams는 Push/Pull 하이브리드

Reactive Streams에서는 다음과 같이 **Push와 Pull**이 결합된 흐름을 가진다.

1. **Pull 요소(Back-Pressure)**  
   - 소비자(Subscriber)는 `request(long n)` 메서드를 호출해 **“n개 만큼 데이터를 받을 준비가 됐다”**고 생산자에게 알린다.  
   - 즉, **소비자가 먼저 수용 가능한 데이터의 양을 “Pull” 방식으로 선언**한다.

2. **Push 요소(onNext 콜백)**  
   - 소비자가 `request(n)`을 호출하면, 생산자(Publisher)는 **실제 데이터 발행**(Push)을 시작한다.  
   - `n`개를 발행하고 나면 더 이상 재요청(Subscriber가 추가 `request(n2)` 호출)하기 전까지는 새 데이터를 보내지 않는다.  
   - 이 때 **생산자가 소비자에게 데이터를 onNext()로 “Push”**한다.

결론적으로, **소비자가 받을 수 있는만큼(`request`)만 발행자(Publisher)가 데이터를 Push**하는 구조이므로, 완전한 Pull 또는 완전한 Push가 아니라, **혼합된 형태의 프로그래밍 모델**이다.

<br>


## 6-3. 하이브리드 모델 방식 이유

- **소비자 측 안전성**: 소비자가 처리할 능력이 안 되는데도, 생산자가 무제한으로 Push해 버리면 메모리나 큐가 폭주(OOM)할 수 있다.  
- **생산자 측 유연성**: 소비자가 데이터를 ‘n개’ 요청하면, 생산자는 그 범위 내에서 데이터를 Push해주면 되고, 추가 요청이 없으면 더 이상 보내지 않아도 된다(리소스 낭비 방지).  
- **비동기 환경**에서 **속도 불균형**(Producer와 Consumer 간)이 발생하기 쉽기 때문에, **Back-Pressure**라는 개념으로 이를 제어하는 것이다.

<br>


## 6-4. 결론

- **이전 방식(동기, 블로킹)**에는 보통 Pull(소비자가 직접 요청) 패턴이 많았다.  
- **순수 Push 모델**(이벤트 스트리밍 등)에서는 소비자가 제어할 방법이 없으니, **데이터 폭주**가 발생할 수 있었다.  
- **Reactive Streams(Flow API)는 Pull + Push가 혼합된 하이브리드**로,  
  - `소비자가 “얼마나 받을 수 있는지”를 Pull 개념으로 요청`하고,  
  - `생산자는 그 범위 안에서 Push하는 방식으로 구현`한다.

즉, Reactive Programming에서 무조건 Push만 하는 것이 아니라, **`소비자 주도로 ‘얼마나 받을지’를 결정(Pull의 요소)`** 하고, 그 후 **`생산자가 데이터를 전달(Push의 요소)`** 하는 **혼합형**이라고 이해하면 된다.

<br><br>

---

천천히