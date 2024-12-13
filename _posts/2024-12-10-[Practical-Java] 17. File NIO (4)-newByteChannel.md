---
layout: post
title:  "[Practical-Java] 17. File NIO (4)-newByteChannel"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Java NIO에서 Files 클래스의 `newByteChannel` 메소드는 `SeekableByteChannel` 인터페이스의 구현체를 반환한다. 이 채널은 파일과의 입출력을 위해 바이트 기반의 채널을 제공하며, 읽기, 쓰기, 파일 위치 변경, 파일 크기 조정 등의 작업을 수행할 수 있다. `SeekableByteChannel`은 Java 7부터 도입된 `java.nio.file` 패키지의 일부로, 기존 `FileChannel`보다 더 유연하고 간단한 API를 제공하여 다양한 파일 작업을 지원한다.

<br>


# 1. Files 클래스의 newByteChannel 메서드

1. **읽기 및 쓰기 지원**: `SeekableByteChannel`은 파일을 읽고 쓰는 기능을 지원하며, 읽기 전용, 쓰기 전용, 읽기/쓰기 모두 가능한 모드로 열 수 있다.
2. **파일 위치 조정**: 채널에서 현재 위치를 조회하거나 변경할 수 있어, 파일의 특정 위치에서 데이터를 읽거나 쓸 수 있다.
3. **파일 크기 조정**: 파일의 크기를 확장하거나 축소할 수 있으며, 이는 파일 내용을 직접 조작할 때 유용하다.
4. **비동기 처리**: NIO의 일부로서, `SeekableByteChannel`은 비동기적으로 파일 작업을 수행할 수 있는 가능성을 제공한다.
5. **다양한 오픈 옵션**: `StandardOpenOption`을 통해 파일을 다양한 모드로 열 수 있다. 예를 들어, `CREATE`, `APPEND`, `TRUNCATE_EXISTING` 등의 옵션을 사용할 수 있다.

<br>


# 2. SeekableByteChannel 인터페이스

`SeekableByteChannel`은 Java NIO 패키지의 일부로 `java.nio.channels.SeekableByteChannel`에 위치하며, `바이트 채널에 대해 읽기, 쓰기, 위치 조정 및 크기 조정과 같은 작업을 수행할 수 있는 인터페이스`이다. 이 인터페이스는 파일이나 파일 유사 리소스에 대한 입출력 작업을 보다 유연하게 제어할 수 있도록 설계되었다. 

`SeekableByteChannel`을 사용하면 파일 포인터를 자유롭게 이동시킬 수 있으며, 이는 파일의 특정 부분에 접근하거나, 파일의 특정 부분을 수정할 때 유용하다. 이 인터페이스는 `java.nio.channels` 패키지 안에 있으며, `java.nio.file.Files` 클래스의 여러 메소드를 통해 구현체를 얻을 수 있다.

<br>

## 2-1. SeekableByteChannel 특징

1. **읽기 및 쓰기 작업 지원**: `SeekableByteChannel`는 데이터를 읽고 쓸 수 있는 기능을 제공한다. 이를 통해 파일 또는 파일 유사 리소스에 대한 입력과 출력이 가능하다.
2. **파일 포인터 조정**: 파일의 현재 위치를 조회하거나 변경할 수 있다. 이 기능은 파일 내에서 임의 접근을 가능하게 하며, 특정 위치에서 읽기나 쓰기를 시작할 수 있게 한다.
3. **파일 크기 변경**: `SeekableByteChannel`을 사용하면 파일 크기를 조정할 수 있다. 이는 파일을 확장하거나 축소하는 작업에 사용될 수 있으며, 파일의 내용을 효율적으로 관리하는 데 도움이 된다.
4. **비동기 작업 지원 가능성**: NIO의 일부로서, 비동기 I/O 작업을 지원한다.
5. **다양한 오픈 옵션**: `Files.newByteChannel` 메소드를 사용할 때 `StandardOpenOption` 열거형을 사용하여 파일을 다양한 방식으로 열 수 있습니다. (읽기 전용, 쓰기 전용, 읽기/쓰기, 파일 생성, 파일 덮어쓰기 등의 옵션을 지정)

이러한 특징으로 대용량 데이터 처리나 복잡한 파일 조작을 필요로 하는 애플리케이션에서 매우 중요한 역할을 한다.

<br>


## 2-2. SeekableByteChannel 인터페이스의 주요 메서드

1. **read(ByteBuffer dst)**
   - 파일로부터 데이터를 읽어 ByteBuffer에 저장한다. 읽은 바이트 수를 반환하며, 파일의 끝에 도달했다면 -1을 반환한다.
   ```java
   int bytesRead = seekableByteChannel.read(buffer);
   ```

2. **write(ByteBuffer src)**
   - ByteBuffer에서 데이터를 읽어 파일에 쓴다. 쓰여진 바이트 수를 반환한다.
   ```java
   int bytesWritten = seekableByteChannel.write(buffer);
   ```

3. **position()**
   - 현재 파일 내의 포인터 위치를 반환한다. 이 위치는 다음 읽기 또는 쓰기 작업이 일어날 위치를 나타낸다.
   ```java
   long currentPosition = seekableByteChannel.position();
   ```

4. **position(long newPosition)**
   - 파일 내의 포인터를 지정된 위치로 설정한다. 이 메서드를 통해 파일 내 임의의 위치로 이동할 수 있다.
   ```java
   seekableByteChannel.position(100); // 100번째 바이트 위치로 이동
   ```

5. **size()**
   - 연결된 파일의 크기를 바이트 단위로 반환한다. 이 크기는 파일에 저장된 데이터의 총량을 나타낸다.
   ```java
   long fileSize = seekableByteChannel.size();
   ```

6. **truncate(long size)**
   - 파일의 크기를 주어진 크기로 줄인다. 이 메서드는 파일의 내용을 줄일 때 사용되며, 지정된 크기 이후의 데이터는 버려진다.
   ```java
   seekableByteChannel.truncate(1024); // 파일 크기를 1024바이트로 조정
   ```

7. **close()**
   - 채널을 닫고 채널이 사용하고 있던 모든 자원을 해제한다. 채널을 닫은 후에는 더 이상 채널을 사용할 수 없다.
   ```java
   seekableByteChannel.close();
   ```



<br>


# 3. `Files.newByteChannel` 메서드, `SeekableByteChannel` 인터페이스 예제 코드

`Files.newByteChannel`를 사용하여 입력 파일과 출력 파일을 위한 `SeekableByteChannel`을 각각 연다. 입력 채널은 **READ** 옵션으로, 출력 채널은 **WRITE** 및 **CREATE** 옵션으로 열린다. **CREATE** 옵션은 해당 경로에 파일이 존재하지 않을 경우 새로 생성하도록 한다.

`ByteBuffer` 인스턴스를 생성하여 파일에서 데이터를 읽고, 읽은 데이터를 출력 파일에 쓰는 데 사용한다. 버퍼의 `allocate` 메소드를 사용하여 1024 바이트 크기의 버퍼를 생성해서 사용한다. ` ByteBuffer는 java.nio 패키지의 채널과 함께 사용되도록 설계되었기 때문이다.` 이 방식은 파일의 크기가 크거나, 파일 내 특정 위치에서 작업을 해야 할 경우 매우 유용하다.


### 파일 처리 예제-1

```java
public class NIOByteChannelExample {

    public static void main(String[] args) {
        Path sourcePath = Paths.get("/Users/example/file_test/file2.txt");  // 입력 파일 경로
        Path targetPath = Paths.get("/Users/example/file_test/file2_copy.txt"); // 출력 파일 경로

        try (SeekableByteChannel sbcIn = Files.newByteChannel(sourcePath, StandardOpenOption.READ);
             SeekableByteChannel sbcOut = Files.newByteChannel(targetPath, StandardOpenOption.WRITE, StandardOpenOption.CREATE)) {
            
            ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
            int bytesRead;

            // 파일에서 데이터 읽기
            while ((bytesRead = sbcIn.read(byteBuffer)) != -1) {
                byteBuffer.flip();      // 버퍼를 읽기 모드로 전환
                sbcOut.write(buffer);   // 읽은 데이터를 출력 파일에 쓰기
                byteBuffer.clear();     // 버퍼를 다시 쓰기 모드로 전환(다음 읽기 작업을 위해 버퍼를 초기화)
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>


### 파일 처리 예제-2

```java
public class NIOSeekableByteChannelExample {
    public static void main(String[] args) {
        Path path = Paths.get("/Users/example/file_test/example.txt");

        // 파일에 데이터 쓰기
        try (SeekableByteChannel sbc = Files.newByteChannel(path, StandardOpenOption.CREATE, StandardOpenOption.WRITE)) {
            ByteBuffer buffer = ByteBuffer.wrap("Hello, File NIO!,\n\nexample.txt file".getBytes());
            sbc.write(buffer);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 파일에서 데이터 읽기
        try (SeekableByteChannel sbc = Files.newByteChannel(path, StandardOpenOption.READ)) {
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            sbc.read(buffer);
            buffer.flip();  // 버퍼를 읽기 모드로 전환
            while (buffer.hasRemaining()) {
                System.out.print((char) buffer.get());
            }
            System.out.println();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>


# 4. ReadableByteChannel과 WritableByteChannel

SeekableByteChannel 인터페이스는 `ReadableByteChannel과 WritableByteChannel는 하위 인터페이스`이다. 그래서 파일을 입출력하는 객체 타입을 의도적으로 각각 명시적으로 객체를 선언 할 수 있다.

```java
try (SeekableByteChannel sbcIn = Files.newByteChannel(sourcePath, StandardOpenOption.READ);
    SeekableByteChannel sbcOut = Files.newByteChannel(targetPath, StandardOpenOption.WRITE, StandardOpenOption.CREATE)) {
```

```java
try (ReadableByteChannel rbcIn = Files.newByteChannel(sourcePath, StandardOpenOption.READ);
    WritableByteChannel wbcOut = Files.newByteChannel(targetPath, StandardOpenOption.WRITE, StandardOpenOption.CREATE)) {
```

<br><br>


---
