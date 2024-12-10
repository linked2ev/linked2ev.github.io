---
layout: post
title:  "[Practical-Java] 15. File NIO (2)-ByteBuffer"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

`ByteBuffer`는 Java NIO의 패키지의 핵심 구성 요소 중 하나로 `바이트 데이터를 읽고, 쓰고, 조작할 수 있는 버퍼를 제공하는 클래스`다. 버퍼는 기본적으로 일련의 데이터 요소를 포함하는 연속된 메모리 블록으로 구성되며, ByteBuffer는 바이트 타입의 데이터를 저장한다. 이 버퍼는 네트워크 통신이나 파일 I/O와 같은 곳에서 데이터를 임시 저장하는 데 사용된다.

<br>


# 1. ByteBuffer 특징

`ByteBuffer는 java.nio 패키지의 채널과 함께 사용되도록 설계되었다.` 그래서 byte[]와 다르게 네트워크 소켓 채널과 파일 채널 등에서 데이터를 효율적으로 읽고 쓰기 위해 사용된다. NIO에서의 채널을 통한 데이터 교환에 필수적이다.

1. **직접 및 간접 버퍼**:
   - `ByteBuffer`는 직접(direct) 또는 간접(non-direct) 버퍼로 생성할 수 있습니다. 직접 버퍼는 운영 체제의 메모리를 사용하여 파일 I/O와 네트워크 I/O를 빠르게 수행할 수 있도록 돕고, 간접 버퍼는 JVM 힙 메모리 위에 생성됩니다.

2. **성능**:
   - 직접 버퍼는 데이터를 네이티브 I/O 작업에 최적화하여 처리하기 때문에 간접 버퍼에 비해 상대적으로 빠른 성능을 제공합니다.

3. **플립(flip) 메서드**:
   - 쓰기에서 읽기로 전환할 때 버퍼를 리셋하여 데이터를 읽을 준비를 할 수 있게 해 줍니다.

4. **복원력**:
   - `mark()`와 `reset()` 메서드를 사용하여 버퍼 내에서 특정 위치를 표시하고, 다시 그 위치로 돌아갈 수 있습니다.

<br>

### 사용 예

```java
ByteBuffer buffer = ByteBuffer.allocate(1024);  // 1024 바이트의 간접 버퍼 생성
buffer.put((byte) 127);                         // 바이트 쓰기
buffer.flip();                                  // 읽기 준비
byte b = buffer.get();                          // 바이트 읽기
```

<br>


# 2. ByteBuffer 클래스의 주요 메서드

1. **allocate(int capacity)**
   - 정적 메서드로, 새로운 버퍼를 생성하고 지정된 용량의 바이트 버퍼를 할당한다. 버퍼는 기본적으로 0으로 초기화된다.
   ```java
   ByteBuffer buffer = ByteBuffer.allocate(1024); // 1024 바이트 크기의 버퍼 생성
   ```

2. **wrap(byte[] array)**
   - 주어진 바이트 배열을 감싸는 새로운 바이트 버퍼를 생성한다. 변경 사항은 버퍼와 배열 모두에 반영된다.
   ```java
   byte[] data = new byte[10];
   ByteBuffer buffer = ByteBuffer.wrap(data);
   ```

3. **put(byte b) / put(byte[] src)**
   - 버퍼의 현재 위치에 바이트를 쓰고 위치를 하나 증가시킨다. 배열을 전달하면 배열의 모든 바이트를 버퍼에 쓸 수 있다.
   ```java
   buffer.put((byte) 127);       // 하나의 바이트 쓰기
   buffer.put(new byte[]{1, 2}); // 바이트 배열 쓰기
   ```

4. **get() / get(byte[] dst)**
   - 버퍼의 현재 위치에서 바이트를 읽고 위치를 하나 증가시킨다. 배열을 인자로 받으면 해당 배열의 크기만큼 데이터를 읽어 배열에 저장한다.
   ```java
   byte b = buffer.get();        // 하나의 바이트 읽기
   byte[] data = new byte[10];
   buffer.get(data);             // 데이터 배열로 읽기
   ```

5. **flip()**
   - 버퍼를 읽기 모드로 전환한다. limit는 현재 position으로 설정되고, position은 0으로 설정된다.
   ```java
   buffer.flip();
   ```

6. **rewind()**
   - 버퍼의 position을 0으로 재설정하여, 데이터를 다시 읽거나 덮어쓸 수 있게 한다.
   ```java
   buffer.rewind();
   ```

7. **clear()**
   - 버퍼를 초기화하여 쓰기 모드로 전환한다. position은 0으로, limit은 capacity와 같게 설정된다.
   ```java
   buffer.clear();
   ```

8. **compact()**
   - 읽지 않은 데이터를 버퍼의 시작 부분으로 이동시키고, position을 마지막으로 이동한 데이터 다음 위치로 설정하여 쓰기 모드로 전환한다.
   ```java
   buffer.compact();
   ```

9. **mark() / reset()**
   - 현재 position을 마크하고 나중에 `reset()` 메서드 호출 시 이 마크 위치로 position을 되돌린다.
   ```java
   buffer.mark();
   buffer.get(); // 데이터 읽기
   buffer.reset(); // 마크한 위치로 돌아감
   ```

10. **remaining()**
    - 현재 position에서 limit까지 남은 요소의 개수를 반환한다.
    ```java
    int remaining = buffer.remaining();
    ```

11. **hasRemaining()**
    - 읽거나 쓸 데이터가 남아있는지를 반환한다. `remaining()`이 0보다 크면 true를 반환한다.
    ```java
    if (buffer.hasRemaining()) {
        byte b = buffer.get();
    }
    ```

<br>

이러한 메서드들을 통해 `ByteBuffer`는 데이터의 저장, 검색, 읽기 및 쓰기 위치 조정 등 다양한 작업을 수행할 수 있게 해 준다. 이는 특히 네트워크 소켓 I/O 또는 파일 I/O에서 유용하게 사용된다.


<br><br>

---