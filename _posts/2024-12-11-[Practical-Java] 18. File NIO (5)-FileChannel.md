---
layout: post
title:  "[Practical-Java] 18. File NIO (5)-FileChannel"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---


대용량 파일을 처리할 때는 FileChannel과 ByteBuffer를 사용하는게 좋다. FileChannel은 자바의 NIO (New Input/Output) 패키지 중 하나로, 파일에서 데이터를 읽고 쓰기 위한 고성능 I/O 기능을 제공한다. `java.nio.channels.FileChannel` 클래스를 사용하여 파일과의 입출력을 효율적으로 관리할 수 있다. 이 채널은 기본 파일 시스템의 파일과 직접 연결되며, 버퍼를 사용하여 데이터를 처리합니다. FileChannel은 비동기적으로도 사용할 수 있어 매우 유용하다.

<br>


# 1. FileChannel 클래스

## ◼︎ FileChannel 주요 특징

1. **읽기와 쓰기**
- **read(ByteBuffer dst)**: 파일로부터 데이터를 읽어 ByteBuffer에 저정한다. 이 메서드는 읽은 바이트 수를 반환하며, 파일 끝에 도달하면 -1을 반환한다.
- **write(ByteBuffer src)**: ByteBuffer에서 데이터를 읽어 파일에 쓰기를 수행한다. 이 메서드는 쓴 바이트 수를 반환한다.

2. **파일 위치 조정**
- **position()**: 현재 파일의 읽기/쓰기 위치를 반환한다.
- **position(long newPosition)**: 파일의 읽기/쓰기 위치를 지정된 위치로 설정한다.

3. **파일 크기 조정**
- **size()**: 파일의 전체 크기를 바이트 단위로 반환한다.
- **truncate(long size)**: 파일을 지정된 크기로 잘라내고 크기를 줄인다.

4. **메모리 매핑**
- **map(FileChannel.MapMode mode, long position, long size)**: 파일의 지정된 부분을 메모리에 매핑한다. 매핑 모드에는 읽기 전용, 쓰기 전용, 읽기/쓰기가 포함된다.

5. **파일 잠금**
- **lock(long position, long size, boolean shared)**: 파일의 지정된 부분에 대한 잠금을 설정한다. `shared`가 `true`이면 공유 잠금, `false`이면 독점 잠금을 의미한다.
- **tryLock(long position, long size, boolean shared)**: 잠금을 시도하고, 잠금이 성공하면 해당 잠금을 반환한다. 잠금을 획득할 수 없는 경우 `null`을 반환한다.

6. **채널 간 데이터 전송**
- **`transferTo(long position, long count, WritableByteChannel target)`**: 현재 `FileChannel`에서 다른 `WritableByteChannel`로 데이터를 직접 전송한다.
- **`transferFrom(ReadableByteChannel src, long position, long count)`**: 다른 `ReadableByteChannel`에서 현재 `FileChannel`로 데이터를 직접 전송한다.

7. **파일 내용 강제 출력**
- **force(boolean metaData)**: 파일의 변경사항을 저장매체에 강제로 쓰도록 한다. `metaData`가 `true`이면 메타데이터의 변경사항까지 저장매체에 기록한다.

8. **비동기 작업 지원**
- **`AsynchronousFileChannel 클래스`** : 비록 FileChannel 자체는 비동기 작업을 지원하지 않지만, AsynchronousFileChannel 클래스를 통해 비슷한 기능을 비동기적으로 수행할 수 있다. 이를 통해 I/O 작업을 논블로킹 방식으로 처리할 수 있으며, 작업 완료 후 콜백을 통해 결과를 처리할 수 있다.

<br>


### ◼︎ FileChannel 클래스를 이용 파일 처리 예제

```java
public class NIOFileChannelExample {

    public static void main(String[] args) {
        Path sourcePath = Paths.get("/Users/example/file_test/file2.txt");  // 입력 파일 경로
        Path targetPath = Paths.get("/Users/example/file_test/file2_copy.txt"); // 출력 파일 경로
        
        try (FileChannel sourceChannel = FileChannel.open(sourcePath, StandardOpenOption.READ);
             FileChannel destChannel = FileChannel.open(targetPath, StandardOpenOption.CREATE, StandardOpenOption.WRITE)) {

            //1MB 크기의 ByteBuffer를 할당하여 파일의 데이터를 버퍼로 읽음
            ByteBuffer buffer = ByteBuffer.allocateDirect(1024 * 1024); // 1MB 버퍼

            while (sourceChannel.read(buffer) != -1) {
                buffer.flip(); // 버퍼를 읽기 모드로 전환
                while (buffer.hasRemaining()) {
                    destChannel.write(buffer); // 버퍼의 내용을 대상 파일에 쓰기
                }
                buffer.clear(); // 버퍼를 다시 쓰기 모드로 전환
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>


# 2. FileChannel 클래스 **transferTo()**와 **transferFrom()** 메소드

FileChannel의 `transferTo()`와 `transferFrom()` 메소드들은 중간 버퍼를 사용하지 않고도 데이터를 직접 채널 간에 전송할 수 있어 더 효율적이다. 이 방법은 중간의 `ByteBuffer`를 사용하지 않고 데이터를 직접 스트림에서 스트림으로 전송하므로, 커널 버퍼를 사용하지 않고 시스템 리소스를 최소화할 수 있다.

- `transferTo()`는 소스 채널에서 목적지 채널로 직접 데이터를 전송함. 반복문은 파일의 전체 크기만큼 데이터가 전송될 때까지 계속 실행
- `transferFrom()`는 소스 채널로부터 목적지 채널로 데이터를 갖고옴. 이 메소드는 내부적으로 `transferTo()`와 유사하게 작동하지만, 메소드 호출 방식이 다름

### ◼︎ **transferTo()**를 사용한 파일 처리 예제

```java
public class NIOFileChannelTransferToExample {
    public static void main(String[] args) {
        Path sourcePath = Paths.get("/Users/example/file_test/file2.txt"); // 입력 파일 경로
        Path targetPath = Paths.get("/Users/example/file_test/file2_copy.txt"); // 출력 파일 경로

        try (FileChannel sourceChannel = FileChannel.open(sourcePath, StandardOpenOption.READ);
             FileChannel destChannel = FileChannel.open(targetPath, StandardOpenOption.CREATE, StandardOpenOption.WRITE)) {

            long position = 0;
            long size = sourceChannel.size();
            while (position < size) {
                position += sourceChannel.transferTo(position, size - position, destChannel);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### ◼︎ **transferFrom()**를 사용한 파일 처리 예제

```java
public class NIOFileChannelTransferFromExample {
    public static void main(String[] args) {
        Path sourcePath = Paths.get("/Users/example/file_test/file2.txt"); // 입력 파일 경로
        Path targetPath = Paths.get("/Users/example/file_test/file2_copy.txt"); // 출력 파일 경로

        try (FileChannel sourceChannel = FileChannel.open(sourcePath, StandardOpenOption.READ);
             FileChannel destChannel = FileChannel.open(targetPath, StandardOpenOption.CREATE, StandardOpenOption.WRITE)) {

            long position = 0;
            long size = sourceChannel.size();
            while (position < size) {
                position += destChannel.transferFrom(sourceChannel, position, size - position);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>

FileChannel 클래스 **transferTo()**와 **transferFrom()** 메소드들은 SocketChannel을 사용하여 파일 데이터를 네트워크 소켓을 통해 전달할 때 유용한거 같다. 서버에서 `ServerSocketChannel`을 열고 특정 포트에서 연결하고 연결이 되면 `SocketChannel`을 통해 데이터를 받아서 처리 하는 방법도 있을 듯 하다. 나중에 실무에서 활용할 날이 있으면 고려해봐야 겠다.

<br>


# 3. AsynchronousFileChannel 클래스 비동기적

Java의 `FileChannel`은 기본적으로 비동기 모드를 직접 지원하지 않는다. 그러나 `AsynchronousFileChannel` 클래스를 사용하여 파일 I/O 작업을 비동기적으로 수행할 수 있다. 이 클래스는 NIO의 일부로서, 파일 채널의 비동기 버전을 제공하여 파일 읽기, 쓰기 등의 작업을 비동기적으로 처리할 수 있도록 도와준다.


### ◼︎ AsynchronousFileChannel 클래스 비동기적 파일 처리 예제 코드

뭔가 내가 원하는 형태로 비동기적으로 처리할려면 ` try-with-resources`를 쓰면 안되는거 같다. 여기서 파일 채널을 닫아서 AsynchronousCloseException 에러가 발생하는 거 같다. 일반적인 try-catch문 사용하니 정상적으로 작동한다.
단순 예제코드라 나중에 사용할 때가 있으면 여러 케이스로 확인이 필요해보인다.

```java
private static void performTask() throws IOException {
    Path sourcePath = Paths.get("/Users/example/file_test/async_file.txt");  // 입력 파일 경로
    Path targetPath = Paths.get("/Users/example/file_test/async_file_copy.txt"); // 출력 파일 경로

    AsynchronousFileChannel readChannel = null;
    AsynchronousFileChannel writeChannel = null;

    try {
        readChannel = AsynchronousFileChannel.open(sourcePath, StandardOpenOption.READ);
        writeChannel = AsynchronousFileChannel.open(targetPath, StandardOpenOption.WRITE, StandardOpenOption.CREATE);

        AsynchronousFileChannel finalReadChannel = readChannel;
        AsynchronousFileChannel finalWriteChannel = writeChannel;

        ByteBuffer buffer = ByteBuffer.allocate(1024);

        readChannel.read(buffer, 0, buffer, new CompletionHandler<Integer, ByteBuffer>() {
            @Override
            public void completed(Integer result, ByteBuffer attachment) {

                if (result > 0) {
                    attachment.flip();
                    finalWriteChannel.write(attachment, 0, attachment, new CompletionHandler<Integer, ByteBuffer>() {
                        @Override
                        public void completed(Integer result, ByteBuffer attachment) {
                            attachment.clear();

                            try {
                                System.out.println("파일 Write ing... 처리시간 10초" );
                                Thread.sleep(10000);
                                System.out.println("파일 Write completed");

                                finalReadChannel.close();
                                finalWriteChannel.close();
                            } catch (IOException | InterruptedException e) {
                                e.printStackTrace();
                            } finally {
                                shutdown();
                            }
                        }

                        @Override
                        public void failed(Throwable exc, ByteBuffer attachment) {
                            System.out.println("파일 Write failed");
                            exc.printStackTrace();
                            shutdown();
                        }
                    });
                } else {
                    System.out.println("파일 Read No data");
                    try {
                        finalReadChannel.close();
                        finalWriteChannel.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    } finally {
                        shutdown();
                    }
                }
            }

            @Override
            public void failed(Throwable exc, ByteBuffer attachment) {
                System.out.println("파일 Read failed");
                exc.printStackTrace();
                shutdown();
            }
        });
    } catch (IOException e) {
        e.printStackTrace();
        if (readChannel != null) {
            readChannel.close();
        }
        if (writeChannel != null) {
            writeChannel.close();
        }
        shutdown();
    }
}
```

<br><br>


---