---
layout: post
title:  "[Practical-Java] 28. java.net.http WebSocket"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Java의 `HttpClient` API에서 제공하는 **WebSocket** 기능은 Java 11에서 추가되었으며, Java 생태계에서 기본 제공하는 WebSocket 클라이언트 구현입니다. 이 기능은 WebSocket 프로토콜(RFC 6455)을 지원하며, 비동기식 메시지 처리 및 HTTP/2 기반의 확장성을 제공합니다.

WebSocket을 사용하는 방법은 `java.net.http.WebSocket` API를 통해 가능합니다. 이 API는 WebSocket 연결을 관리하고, 메시지의 송수신을 비동기적으로 처리할 수 있도록 설계되었습니다. 외부 라이브러리 없이도 간단한 WebSocket 통신을 처리할 수 있다는 점에서 큰 장점이다. **간단한 클라이언트 애플리케이션**이나 **Java 표준 API 사용**이 목표인 경우 `HttpClient` WebSocket API로 많이 사용된다.

<br>


# 1. WebSocket

## 1-1. `HttpClient` WebSocket

**Java 11**에 `HttpClient` API가 표준으로 도입되고, WebSocket 클라이언트 기능이 추가
- WebSocket 클라이언트를 기본 API로 제공함으로써, 외부 라이브러리 없이도 WebSocket 통신을 구현 가능
- RFC 6455(WebSocket 프로토콜 표준)를 기반으로 구현
- 이후 버전에서는 성능 최적화와 새로운 프로토콜 확장(HTTP/2, HTTP/3 통합) 등이 점진적으로 개선

<br>


## 1-2. 특징 및 장점

1. **표준 WebSocket 프로토콜 지원**  
   - RFC 6455(WebSocket Protocol)를 준수하여 동작
   - HTTP 요청을 통해 WebSocket으로 업그레이드 가능
   - 
1. **표준 Java API 제공**  
   - 외부 라이브러리 없이 Java 11 이상에서 기본적으로 WebSocket 클라이언트를 구현 가능
   - 의존성 관리가 쉬움

2. **비동기 처리 지원**  
   -  WebSocket API는 **비동기식 프로그래밍 모델**을 기반으로 설계되어, 메시지 송수신 시 `CompletionStage`와 같은 Java의 비동기 도구를 활용
   - `CompletableFuture`와 `CompletionStage`를 활용하여 효율적인 비동기 메시지 송수신 구현 가능

3. **성능 최적화**  
   - Java의 최신 HTTP/2 및 NIO 기반 구현으로 높은 성능을 제공
   - WebSocket의 지속적인 연결 특성을 활용하여 네트워크 오버헤드 감소

4. **쉬운 통합**  
   - Java 11의 `HttpClient` API와 통합되어, HTTP와 WebSocket 통신을 단일 API로 처리 가능
   - HTTP 요청-응답과 WebSocket 연결을 하나의 API로 처리 가능
   - 서버와 클라이언트 간 양방향 통신에 적합

5. **보안 강화**  
   - HTTPS 기반의 TTLS 의 지원으로 `wss://`를 통해 WebSocket 보안 연결(TLS)을 기본적으로 지원
   - TLS 및 인증 메커니즘과 자연스럽게 연동

6. **범용성**  
   - Java 생태계와의 자연스러운 통합(예: Jakarta EE, Spring Framework)
   - HTTP/2, HTTP/3과 같은 최신 HTTP 프로토콜과 통합 지원

<br>


## 1-3. 단점

1. **서버 기능 미지원**  
   - `HttpClient` WebSocket은 클라이언트 전용 API이며, 서버 기능은 제공하지 않습니다. (Java에서 WebSocket 서버를 구현하려면 Jakarta EE, Spring WebSocket 등 추가 프레임워크가 필요)

2. **기능의 단순함**  
   - 고급 기능(STOMP 프로토콜 지원, 클러스터링 등)은 제공하지 않음
   - 대규모 애플리케이션에서는 제한적일 수 있음

3. **Java 11 이상 필요**  
   - Java 11 이상에서만 사용 가능하며, Java 8 이하의 프로젝트에서는 사용할 수 없음

4. **추상화 부족**  
   - Raw WebSocket 메시지(텍스트 또는 바이너리)를 처리해야 하므로, JSON, STOMP와 같은 메시지 프로토콜을 직접 구현해야 하는 부담이 있음

<br><br>


# 2. Java HttpClient WebSocket 클라이언트 예제

## 2-1. 자바11의 java.net.http WebSocket 클라이언트 호출


```java
// 1. HttpClient 생성
HttpClient client = HttpClient.newHttpClient();

// 2. WebSocket.Builder로 WebSocket 생성
WebSocket webSocket = client.newWebSocketBuilder()
        .buildAsync(URI.create("ws://localhost:8080/endpoint"), new WebSocketListener())
        .join(); // 비동기 작업 완료 대기

// 3. 서버로 메시지 전송
webSocket.sendText("Hello, WebSocket Server!", true);

// 4. WebSocket을 닫기 전에 대기 (예시용)
Thread.sleep(5000);
webSocket.sendClose(WebSocket.NORMAL_CLOSURE, "Closing WebSocket");
```

<br>


## 2-2. 멀티 쓰레드 형태 WebSocket 클라이언트 호출 예제

실시간 통신에서 주로 사용하는하기에 아래 예제가 딱히 활용할만한 예제는 아니고 너무 그냥 구현함

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.*;

/**
 * 자바11의 java.net.http WebSocket 예제
 */
public class HttpClientWebSocketExample {

    private static final int THREAD_COUNT = 3;
    private static ExecutorService executor = Executors.newFixedThreadPool(THREAD_COUNT, new ThreadFactory() {
        private int threadCounter = 0;

        @Override
        public Thread newThread(Runnable r) {
            Thread thread = new Thread(r);
            thread.setName("WebSocket-Thread-" + threadCounter++);
            System.out.println("# 신규 실행자 생성 : " + thread.getName() + ", 스레드 그룹 : " + thread.getThreadGroup());
            return thread;
        }
    });

    public static void main(String[] args) throws Exception {
        // 멀티 스레드 기반의 HTTP 클라이언트 생성
        HttpClient httpClient = HttpClient.newBuilder().executor(executor).build();

        List<CompletableFuture<Void>> futures = new ArrayList<>();

        for (int i = 0; i < THREAD_COUNT; i++) {
            int threadId = i;
            //비동기로 웹소켓 전송
            futures.add(createAndSendWebSocketMessage(httpClient, threadId));
        }
        // 모든 WebSocket 작업 완료 대기
        CompletableFuture.allOf(futures.toArray(new CompletableFuture[0])).join();

        // ExecutorService 종료 대기
        executor.shutdown();
        executor.awaitTermination(10, TimeUnit.SECONDS);
    }

    private static CompletableFuture<Void> createAndSendWebSocketMessage(HttpClient httpClient, int threadId) {
        return httpClient.newWebSocketBuilder()
                .buildAsync(URI.create("ws://localhost:8090"), new WebSocketListener(threadId))
                .thenCompose(webSocket -> webSocket.sendText("클라이언트 웹소켓 통신 쓰레드:" + threadId , true)
                        .thenRun(() -> System.out.println("Thread " + threadId + " 메시지 전송 완료"))
                        .thenCompose(v -> webSocket.sendClose(WebSocket.NORMAL_CLOSURE, "Thread " + threadId + " 종료")
                                .thenRun(() -> System.out.println("Thread " + threadId + " WebSocket 연결 종료"))));
//                .thenApply(webSocket -> {
//                    webSocket.sendText("클라이언트 웹소켓 통신 쓰레드:" + threadId , true)
//                            .thenRun(() -> System.out.println("메시지 전송 완료"));
//
//                    webSocket.sendClose(WebSocket.NORMAL_CLOSURE, "ok")
//                            .thenRun(() -> System.out.println("WebSocket 연결 종료"));
//
//                    return webSocket;
//                })
//                .exceptionally(e -> {
//                    System.err.println("WebSocket 작업 중 오류: " + e.getMessage());
//                    return null;
//                });
    }
}

/**
 * WebSocket 리스너 구현
 */
class WebSocketListener implements WebSocket.Listener {
    private final int threadId;

    public WebSocketListener(int threadId) {
        this.threadId = threadId;
    }

    /**
     * WebSocket 연결이 열렸을 때 실행
     */
    @Override
    public void onOpen(WebSocket webSocket) {
        System.out.println("Thread " + threadId + " WebSocket 연결 열림");
        WebSocket.Listener.super.onOpen(webSocket);
    }

    /**
     * 서버로부터 메시지를 수신했을 때 실행
     */
    @Override
    public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
        System.out.println("Thread " + threadId + " 수신 메시지 = " + data);
        return WebSocket.Listener.super.onText(webSocket, data, last);
    }

    /**
     * WebSocket 연결이 닫혔을 때 실행
     */
    @Override
    public CompletionStage<?> onClose(WebSocket webSocket, int statusCode, String reason) {
        System.out.println("Thread " + threadId + " WebSocket 연결 닫힘. 상태코드: " + statusCode + ", 이유: " + reason);
        return WebSocket.Listener.super.onClose(webSocket, statusCode, reason);
    }

    /**
     * WebSocket 에러 발생 시 실행
     */
    @Override
    public void onError(WebSocket webSocket, Throwable error) {
        System.err.println("Thread " + threadId + " WebSocket 에러: " + error.getMessage());
        WebSocket.Listener.super.onError(webSocket, error);
    }
}
```

```
# 신규 실행자 생성 : WebSocket-Thread-0, 스레드 그룹 : java.lang.ThreadGroup[name=main,maxpri=10]
# 신규 실행자 생성 : WebSocket-Thread-1, 스레드 그룹 : java.lang.ThreadGroup[name=main,maxpri=10]
# 신규 실행자 생성 : WebSocket-Thread-2, 스레드 그룹 : java.lang.ThreadGroup[name=main,maxpri=10]
Thread 1 WebSocket 연결 열림
Thread 2 WebSocket 연결 열림
Thread 0 WebSocket 연결 열림
Thread 2 메시지 전송 완료
Thread 1 메시지 전송 완료
Thread 0 메시지 전송 완료
Thread 2 수신 메시지 = [WebsocketServer] 서버에서 반송: 2번 쓰레드의 응답 데이터
Thread 1 수신 메시지 = [WebsocketServer] 서버에서 반송: 1번 쓰레드의 응답 데이터
Thread 0 수신 메시지 = [WebsocketServer] 서버에서 반송: 0번 쓰레드의 응답 데이터
Thread 0 WebSocket 연결 종료
Thread 2 WebSocket 연결 종료
Thread 1 WebSocket 연결 종료
```

<br><br>


# 3. WebSocket.Listener 주요 인터페이스

- WebSocket API는 `java.net.http.WebSocket` 패키지의 일부로 `WebSocket.Listener`는 WebSocket 통신 이벤트를 처리하기 위한 인터페이스이다.
- 주요 메서드는 텍스트, 바이너리 메시지, Ping/Pong, 연결 종료, 오류 등을 처리할 수 있다.
- 각 메서드는 기본적으로 비동기 작업(`CompletionStage<?>`)을 반환하여 효율적인 처리가 가능하다.

<br>


### ① `onOpen (WebSocket webSocket)`
- **설명**: WebSocket 연결이 성공적으로 열렸을 때 호출
- **사용 예시**: 초기화 작업, 클라이언트-서버 간 초기 메시지 교환
- **반환값**: `void`
- **예제**:
  ```java
  @Override
  public void onOpen(WebSocket webSocket) {
      System.out.println("WebSocket 연결 열림");
      WebSocket.Listener.super.onOpen(webSocket);
  }
  ```

---

### ② `onText (WebSocket webSocket, CharSequence data, boolean last)`
- **설명**: 서버로부터 **텍스트 메시지**를 수신했을 때 호출
- **파라미터**:
  - `webSocket`: WebSocket 객체
  - `data`: 수신된 텍스트 메시지
  - `last`: 메시지가 다중 프레임으로 나뉘었을 경우, 현재 프레임이 마지막인지 여부
- **반환값**: `CompletionStage<?>` (비동기 작업을 반환하거나 기본 동작을 호출 가능)
- **예제**:
  ```java
  @Override
  public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
      System.out.println("수신 텍스트 메시지: " + data);
      return WebSocket.Listener.super.onText(webSocket, data, last);
  }
  ```

---

### ③ `onBinary (WebSocket webSocket, ByteBuffer data, boolean last)`
- **설명**: 서버로부터 **바이너리 메시지**를 수신했을 때 호출됩니다.
- **파라미터**:
  - `webSocket`: WebSocket 객체
  - `data`: 수신된 바이너리 데이터
  - `last`: 메시지가 다중 프레임으로 나뉘었을 경우, 현재 프레임이 마지막인지 여부
- **반환값**: `CompletionStage<?>`
- **예제**:
  ```java
  @Override
  public CompletionStage<?> onBinary(WebSocket webSocket, ByteBuffer data, boolean last) {
      String textMessage = new String(data.array(), StandardCharsets.UTF_8);
      System.out.println("수신 바이너리 메시지: " + textMessage);
      return WebSocket.Listener.super.onBinary(webSocket, data, last);
  }
  ```

---

### ④ `onPing (WebSocket webSocket, ByteBuffer message)`
- **설명**: 서버가 **Ping 프레임**을 보냈을 때 호출
- **파라미터**:
  - `webSocket`: WebSocket 객체
  - `message`: Ping 데이터
- **반환값**: `CompletionStage<?>`
- **예제**:
  ```java
  @Override
  public CompletionStage<?> onPing(WebSocket webSocket, ByteBuffer message) {
      System.out.println("Ping 메시지 수신");
      return WebSocket.Listener.super.onPing(webSocket, message);
  }
  ```

---

### ⑤ `onPong(WebSocket webSocket, ByteBuffer message)`
- **설명**: 서버가 **Pong 프레임**을 보냈을 때 호출
- **파라미터**:
  - `webSocket`: WebSocket 객체
  - `message`: Pong 데이터
- **반환값**: `CompletionStage<?>`
- **예제**:
  ```java
  @Override
  public CompletionStage<?> onPong(WebSocket webSocket, ByteBuffer message) {
      System.out.println("Pong 메시지 수신");
      return WebSocket.Listener.super.onPong(webSocket, message);
  }
  ```

---

### ⑥ `onClose(WebSocket webSocket, int statusCode, String reason)`
- **설명**: WebSocket 연결이 종료되었을 때 호출
- **파라미터**:
  - `webSocket`: WebSocket 객체
  - `statusCode`: 종료 상태 코드
  - `reason`: 종료 이유
- **반환값**: `CompletionStage<?>`
- **예제**:
  ```java
  @Override
  public CompletionStage<?> onClose(WebSocket webSocket, int statusCode, String reason) {
      System.out.println("WebSocket 연결 닫힘. 상태코드: " + statusCode + ", 이유: " + reason);
      return WebSocket.Listener.super.onClose(webSocket, statusCode, reason);
  }
  ```

---

### ⑦ `onError(WebSocket webSocket, Throwable error)`
- **설명**: WebSocket 통신 중 오류가 발생했을 때 호출
- **파라미터**:
  - `webSocket`: WebSocket 객체
  - `error`: 발생한 예외
- **반환값**: `void`
- **예제**:
  ```java
  @Override
  public void onError(WebSocket webSocket, Throwable error) {
      System.err.println("WebSocket 에러 발생: " + error.getMessage());
  }
  ```

<br><br>

---
