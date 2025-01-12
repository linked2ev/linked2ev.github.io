---
layout: post
title:  "[Practical-Java] 27. HttpClient"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

웹이 거의 모든 애플리케이션의 표준 개발 및 운영 환경으로 자리잡으면서 `HTTP(Hyper Text Transfer Protocol)`는 가장 간단하면서도 가장 중요한 프로토콜로 자리 잡았다. 많은 프로그래밍 언어들은 HTTP 프로토콜을 지원하기 위해 자체 언어 API 내에 HTTP 클라이언트 기능을 제공하고 있다.

자바의 최초 버전인 1.0에서는 URL과 URLConnection 클래스를 통해 Http 지원했다. 1.1버전부터 HttpUrlConnection 클래스를 공식적으로 지원했다. 1.4버전에서 부터 HTTPS/SSL 기능을 제공하는 HttpsUrlConnection 이 추가되었다. Http 클라이언트를 지원하기 위해 외부 라이브러리들도 있다.

<br><br>


# 1. 초기 HTTP 통신 `HttpURLConnection`

### 등장 배경

- 자바는 1990년대 초반에 등장하여 인터넷 애플리케이션 개발을 주요 목적
- 초기 자바 버전에서는 네트워크 통신을 쉽게 처리할 수 있도록 다양한 클래스가 포함. 대표적으로 **`java.net`** 패키지에서 제공되는 **`URL`** 및 **`HttpURLConnection`** 클래스가 포함
- 이 클래스들은 HTTP 요청을 생성하고 응답을 처리하는 데 사용

### `HttpURLConnection`의 특징

- **장점**:
  - 간단한 HTTP 요청/응답 기능을 제공
  - GET, POST와 같은 기본 HTTP 메서드 지원
  - 표준 자바 라이브러리의 일부로 추가 라이브러리가 필요 없음

- **단점**:
  - 구현이 다소 저수준(low-level)으로 복잡하고, 매 요청 시 많은 설정 필요
  - 비효율적인 연결 관리
  - 동기식 요청만 지원하며, 비동기식 요청을 처리하기 어려움

<br>

## ◼︎ HttpURLConnection GET 요청

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class HttpURLConnectionGetExample {

    public static void main(String[] args) throws Exception {
        URL url = new URL("https://jsonplaceholder.typicode.com/posts/1");
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("GET");

        int responseCode = connection.getResponseCode();
        System.out.println("Response Code: " + responseCode);

        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        String inputLine;
        StringBuilder response = new StringBuilder();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }
        in.close();

        System.out.println("Response: " + response.toString());
    }
}
```

<br>

## ◼︎ HttpURLConnection POST 요청

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class HttpURLConnectionPostExample {

    public static void main(String[] args) throws Exception {
        URL url = new URL("https://jsonplaceholder.typicode.com/posts");
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setDoOutput(true);

        String jsonInputString = "{\"title\": \"foo\", \"body\": \"bar\", \"userId\": 1}";
        try (OutputStream os = connection.getOutputStream()) {
            byte[] input = jsonInputString.getBytes("utf-8");
            os.write(input, 0, input.length);
        }

        int responseCode = connection.getResponseCode();
        System.out.println("Response Code: " + responseCode);

        try (BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream(), "utf-8"))) {
            StringBuilder response = new StringBuilder();
            String responseLine;
            while ((responseLine = br.readLine()) != null) {
                response.append(responseLine.trim());
            }
            System.out.println("Response: " + response.toString());
        }
    }
}
```

---

<br>


# 2. HTTP 클라이언트 라이브러리 등장

### 배경

- 2000년대 초반, 자바로 대규모 애플리케이션을 개발하는 사례가 늘면서 **`HttpURLConnection`**의 한계
- 예를 들어, 고급 HTTP 기능(쿠키 관리, 다중 요청/응답 처리, SSL 지원)을 효율적으로 처리하려면 추가적인 코드 작성이 필요
- 이를 보완하기 위해 Apache 재단과 같은 커뮤니티에서 새로운 HTTP 클라이언트 라이브러리가 등장 
- HttpURLConnection 이후 많은 기업과 개발자들이 **Apache HttpClient**를 사용하여 HTTP 요청 처리


## 2-1. Apache HttpClient (이후 Apache HttpComponents)

- 다양한 HTTP 메서드 지원(GET, POST, PUT, DELETE 등)로 REST API에 유용
- 쿠키, 인증, 프록시 지원
- 연결 풀(Connection Pool)을 통해 효율적인 자원 관리
- 비동기 요청 처리 (Apache HttpAsyncClient)

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>{버젼}</version>
</dependency>
```

```gradle
dependencies {
    implementation 'org.apache.httpcomponents:httpclient:{버젼}'
}
```

<br>


## ◼︎ Apache HTTP GET 요청

```java
import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class ApacheHttpGetExample {

    public static void main(String[] args) throws Exception {
        String url = "https://jsonplaceholder.typicode.com/posts/1";
        // HttpClient 생성
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            // GET 요청 생성
            HttpGet request = new HttpGet(url);
            // 요청 실행 및 응답 받기
            try (CloseableHttpResponse response = httpClient.execute(request)) {
                System.out.println("Response Code: " + response.getStatusLine().getStatusCode());
                // 응답 내용 출력
                HttpEntity entity = response.getEntity();
                if (entity != null) {
                    String result = EntityUtils.toString(entity);
                    System.out.println("Response: " + result);
                }
            }
        }
    }
}
```

<br>


## ◼︎ Apache HTTP POST 요청

```java
import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class ApacheHttpPostExample {

    public static void main(String[] args) throws Exception {
        // URL 설정
        String url = "https://jsonplaceholder.typicode.com/posts";
        // JSON 데이터 설정
        String jsonInputString = "{\"title\": \"foo\", \"body\": \"bar\", \"userId\": 1}";

        // HttpClient 생성
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            // POST 요청 생성
            HttpPost postRequest = new HttpPost(url);
            postRequest.setHeader("Content-Type", "application/json");

            // JSON 데이터를 요청 본문에 추가
            StringEntity stringEntity = new StringEntity(jsonInputString);
            postRequest.setEntity(stringEntity);

            // 요청 실행 및 응답 받기
            try (CloseableHttpResponse response = httpClient.execute(postRequest)) {
                System.out.println("Response Code: " + response.getStatusLine().getStatusCode());
                // 응답 내용 출력
                HttpEntity entity = response.getEntity();
                if (entity != null) {
                    String result = EntityUtils.toString(entity);
                    System.out.println("Response: " + result);
                }
            }
        }
    }
}
```

<br>


## 2-2. OkHttp

- OkHttp는 Square의 엔지니어링 팀에서 주도적으로 개발한 라이브러리로 Square의 주요 오픈 소스 프로젝트 중 하나로, 간단하고 효율적인 HTTP 클라이언트를 제공하기 위해 설계
- Netflix, Square 등에서 사용되며, 경량 HTTP 클라이언트 라이브러리로 유명
- 비동기 요청 처리와 효율적인 캐싱 지원
- OkHttp는 특히 성능, 안정성, HTTP/2 및 WebSocket 지원에서 강점

```xml
<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
    <version>{버젼}</version>
</dependency>
```

```gradle
dependencies {
   implementation 'com.squareup.okhttp3:okhttp:{버젼}'
}
```

<br>


## ◼︎ OkHttp HTTP GET 요청

```java
import okhttp3.*;

import java.io.IOException;

public class OkHttpGetExample {

    public static void main(String[] args) throws Exception {
        // OkHttpClient 생성
        OkHttpClient client = new OkHttpClient();

        // GET 요청 생성
        Request request = new Request.Builder()
                .url("https://jsonplaceholder.typicode.com/posts/1")
                .build();

        // 요청 실행 및 응답 처리
        try (Response response = client.newCall(request).execute()) {
            System.out.println("Response Code: " + response.code());
            if (response.body() != null) {
                System.out.println("Response: " + response.body().string());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>


## ◼︎ OkHttp HTTP POST 요청

```java
import okhttp3.*;

import java.io.IOException;

public class OkHttpPostExample {

    public static void main(String[] args) throws Exception {
        // OkHttpClient 생성
        OkHttpClient client = new OkHttpClient();

        // JSON 데이터 생성
        String jsonInputString = "{\"title\": \"foo\", \"body\": \"bar\", \"userId\": 1}";

        // RequestBody 생성
        RequestBody body = RequestBody.create(
                jsonInputString, MediaType.parse("application/json"));

        // POST 요청 생성
        Request request = new Request.Builder()
                .url("https://jsonplaceholder.typicode.com/posts")
                .post(body)
                .build();

        // 요청 실행 및 응답 처리
        try (Response response = client.newCall(request).execute()) {
            System.out.println("Response Code: " + response.code());
            if (response.body() != null) {
                System.out.println("Response: " + response.body().string());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>

---


## 3. `HttpClient` (Java 9 및 Java 11)

### 등장 배경

- java 진영에서는 빠른 변화에 맞춰 기존의 HttpUrlConnection 클래스의 개선으로는 한계로 java11부터 공식적으로 `java.net.http` 로 HTTP Client를 지원
- HTTP/2 프로토콜의 등장과 기존 HTTP 클라이언트 구현의 비효율성이 주요 이유
- Java 9에서 실험적으로 **`HttpClient`** API가 등장했고, Java 11에서 정식 API로 추가

### 주요 특징

1. **HTTP/2 지원**:
   - 이전 HTTP/1.1과 비교해 더 나은 성능과 효율성 제공
   - 멀티플렉싱(multiplexing)을 통해 단일 연결에서 여러 요청/응답 처리 가능

2. **비동기 요청 지원**:
   - **`CompletableFuture`** 및 **Reactive Streams**를 사용해 비동기 프로그래밍 가능
   - 서버-클라이언트 간의 효율적인 비동기 데이터 처리

3. **구조적 단순화**:
   - 코드 작성이 간결
   - 기존 **`HttpURLConnection`**의 복잡한 설정 과정을 간소화

4. **Builder 패턴**:
   - 요청 객체 생성 시 유연한 빌더 패턴 지원
   - 예를 들어, 헤더 설정, 메서드 설정 등을 간편하게 처리 가능

5. **표준화**:
   - 표준 자바 라이브러리에 포함되므로 별도의 외부 라이브러리 의존성을 줄임

<br>


## ◼︎ HttpClient GET 요청

### 동기 요청

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class HttpClientGetExample {

    public static void main(String[] args) throws Exception {
        // HttpRequest 생성
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts/1"))
                .GET()
                .build();
        // HttpClient 생성
        HttpClient client = HttpClient.newHttpClient();
        // 요청
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        
        System.out.println("Response Code: " + response.statusCode());
        System.out.println("Response: " + response.body());
    }
}
```

### 비동기 요청

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.concurrent.CompletableFuture;

public class HttpClientAsyncExample {

    public static void main(String[] args) {
        // HttpRequest 생성
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts/1"))
                .GET()
                .build();
        // HttpClient 생성
        HttpClient client = HttpClient.newHttpClient();
        // 비동기 요청
        CompletableFuture<HttpResponse<String>> futureResponse = client.sendAsync(request, HttpResponse.BodyHandlers.ofString());

        // 비동기 처리 결과 출력
        // - thenAccept : Consumer<T> (결과를 소비만 함), 단순히 결과를 처리
        // - thenApply : Function<T, R> (결과를 변환 후 반환), 결과를 변환하거나 추가 연산
        futureResponse.thenAccept(response -> {
            System.out.println("Response Code: " + response.statusCode());
            System.out.println("Response: " + response.body());
        }).exceptionally(e -> {
            System.err.println("Error occurred: " + e.getMessage());
            return null;
        });

        try {
            Thread.sleep(5000); // 비동기 작업 완료까지 대기
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

<br>


## ◼︎ HttpClient POST 요청

### 동기 요청

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class HttpClientPostExample {

    public static void main(String[] args) throws Exception {
        // HttpClient 생성
        HttpClient client = HttpClient.newHttpClient();
        // 요청 JSON 데이터
        String jsonInputString = "{\"title\": \"foo\", \"body\": \"bar\", \"userId\": 1}";
        // HttpRequest 생성
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts"))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(jsonInputString))
                .build();
        // 요청
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        System.out.println("Response Code: " + response.statusCode());
        System.out.println("Response: " + response.body());
    }
}
```

### 비동기 요청

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.concurrent.CompletableFuture;

public class HttpClientPostAsyncExample {

    public static void main(String[] args) {
        // HttpClient 생성
        HttpClient client = HttpClient.newHttpClient();

        String jsonInputString = "{\"title\": \"foo\", \"body\": \"bar\", \"userId\": 1}";
        // HttpRequest 생성
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts"))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(jsonInputString))
                .build();
        // 비동기 요청
        CompletableFuture<HttpResponse<String>> futureResponse = client.sendAsync(request, HttpResponse.BodyHandlers.ofString());
        // 응답 처리
        futureResponse.thenAccept(response -> {
            System.out.println("Response Code: " + response.statusCode());
            System.out.println("Response: " + response.body());
        }).exceptionally(e -> {
            System.err.println("Error occurred: " + e.getMessage());
            return null;
        });

        try {
            Thread.sleep(5000); // 비동기 작업 완료까지 대기
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

<br>


## 3-1. `HttpResponse.BodyHandlers` HTTP 응답 본문

### 1) **`ofString()`**

HTTP 응답 본문을 `String`으로 처리, 텍스트 데이터(JSON, XML, HTML 등)를 처리할 때 사용

```java
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
String responseBody = response.body();
```

<br>

### 2) **`ofByteArray()`**

HTTP 응답 본문을 `byte[]`로 처리, 바이너리 데이터를 처리할 때 사용 (예: 이미지, 파일 다운로드)

```java
HttpResponse<byte[]> response = client.send(request, HttpResponse.BodyHandlers.ofByteArray());
byte[] responseBody = response.body();
```

<br>

### 3) **`ofFile (Path filePath)`**

HTTP 응답 본문을 지정된 파일 경로에 저장, 대용량 데이터 다운로드 및 파일 저장

```java
HttpResponse<Path> response = client.send(request, HttpResponse.BodyHandlers.ofFile(Paths.get("response.txt")));
Path filePath = response.body();
```

<br>

### 4) **`ofInputStream()`**

HTTP 응답 본문을 `InputStream`으로 처리. 스트리밍 방식으로 데이터를 처리할 때 사용

```java
HttpResponse<InputStream> response = client.send(request, HttpResponse.BodyHandlers.ofInputStream());
try (InputStream is = response.body()) {
   // InputStream으로 데이터 처리
}
```

<br>

### 5) **`fromLineSubscriber (Flow.Subscriber<String> subscriber)`**

응답 본문을 줄 단위로 읽어 `Subscriber`로 전달. 라인 단위 데이터 처리 (예: 로그, 실시간 데이터 스트림)

```java
HttpResponse<Void> response = client.send(request, HttpResponse.BodyHandlers.fromLineSubscriber(new MyLineSubscriber()));
```

<br>

### 6) **`fromSubscriber (Flow.Subscriber<List<ByteBuffer>> subscriber)`**

HTTP 응답 본문을 `ByteBuffer` 목록으로 처리하며 `Subscriber`에 전달한다. 사용자 정의 방식으로 데이터를 처리.

```java
HttpResponse<Void> response = client.send(request, HttpResponse.BodyHandlers.fromSubscriber(new MyByteBufferSubscriber()));
```

<br>

### 7) **`discarding()`**

HTTP 응답 본문을 무시한다. 본문 내용이 필요하지 않을 때 사용

```java
HttpResponse<Void> response = client.send(request, HttpResponse.BodyHandlers.discarding());
```

<br>


## 3-2. `HttpRequest.BodyPublishers`는 HTTP 요청 본문

### 1) **`ofString (String)`**

요청 본문을 `String`으로 생성. 텍스트 데이터(JSON, XML, HTML 등)를 요청 본문에 포함할 때 사용

```java
HttpRequest request = HttpRequest.newBuilder()
         .uri(URI.create("https://example.com"))
         .POST(HttpRequest.BodyPublishers.ofString("{\"key\":\"value\"}"))
         .build();
```

<br>

### 2) **`ofByteArray(byte[])`**

요청 본문을 바이너리 데이터를 요청 본문에 포함할 때 사용 (예: 이미지, 파일 업로드)

```java
byte[] data = "Hello, World!".getBytes();
HttpRequest request = HttpRequest.newBuilder()
         .uri(URI.create("https://example.com"))
         .POST(HttpRequest.BodyPublishers.ofByteArray(data))
         .build();
```

<br>

### 3) **`ofFile (Path filePath)`**

요청 본문을 지정된 파일로 생성, 파일을 HTTP 요청 본문으로 전송할 때 사용 (예: 파일 업로드)

```java
HttpRequest request = HttpRequest.newBuilder()
         .uri(URI.create("https://example.com"))
         .POST(HttpRequest.BodyPublishers.ofFile(Paths.get("file.txt")))
         .build();
```

<br>

### 4) **`ofInputStream (Supplier<InputStream>)`**

요청 본문을 `InputStream`으로 생성. 스트리밍 방식으로 데이터를 요청 본문에 포함할 때 사용

```java
HttpRequest request = HttpRequest.newBuilder()
         .uri(URI.create("https://example.com"))
         .POST(HttpRequest.BodyPublishers.ofInputStream(() -> new ByteArrayInputStream("Hello".getBytes())))
         .build();
```

<br>

### 5) **`fromPublisher (Flow.Publisher<ByteBuffer>)`**

사용자 정의 `Publisher`를 사용하여 본문 생성. 사용자 정의 데이터 소스를 요청 본문으로 전송

```java
HttpRequest request = HttpRequest.newBuilder()
         .uri(URI.create("https://example.com"))
         .POST(HttpRequest.BodyPublishers.fromPublisher(new MyPublisher()))
         .build();
```

<br>

### 6) `URL 방식 form data` http post 요청 방식

```java
Map<String, String> formData = getFormData();

// 폼 데이터를 URL 인코딩
String encodedFormData = formData.entrySet().stream()
         .map(entry -> URLEncoder.encode(entry.getKey(), StandardCharsets.UTF_8) + "=" 
                     + URLEncoder.encode(entry.getValue(), StandardCharsets.UTF_8))
         .collect(Collectors.joining("&"));
// HTTP 요청 생성
HttpRequest request = HttpRequest.newBuilder()
         .uri(URI.create("https://example.com/login"))
         .header("Content-Type", "application/x-www-form-urlencoded")
         .POST(HttpRequest.BodyPublishers.ofString(encodedFormData))
         .build();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
```

<br><br>

---
