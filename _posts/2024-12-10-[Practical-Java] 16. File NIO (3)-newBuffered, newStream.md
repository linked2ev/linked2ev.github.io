---
layout: post
title:  "[Practical-Java] 16. File NIO (3)-newBuffered, newStream"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바 7이후로 나온 File NIO에 대해서 `Files 클래스의 newBuffered, newInputStream/newOutputStream 메서드`를 사용해 파일을 Read, Write 
하는 설명하는 포스팅이다. Java File I/O의 기본적인 처리에 대한 자세한 내용은 없다. (참고 : [[Java] FILE (1) 입출력 - Stream(스트림)](https://linked2ev.github.io/java/2021/05/02/Java-FILE-(1)-%EC%9E%85%EC%B6%9C%EB%A0%A5-Stream(%EC%8A%A4%ED%8A%B8%EB%A6%BC)/))

<br>


# 1. File NIO newBuffered Reader/Writer

Files 클래스를 newBuffered 를 이용한 파일을 Read하고 Wrtie 하는 예제 코드이다. Files.newBufferedReader와 Files.newBufferedWriter는 텍스트 파일을 처리하기 위한 메소드이다. 이 객체들은 내부적으로 버퍼를 사용하여 파일 읽기 및 쓰기 작업의 효율성을 높여준다.

```java
public class NIOBufferedFileExample {
    public static void main(String[] args) {
        String sourcePath = "/Users/example/file_test/file1.txt";  // 입력 파일 경로
        String targerPath = "/Users/example/file_test/file1_copy.txt"; // 출력 파일 경로

        try (BufferedReader reader = Files.newBufferedReader(Paths.get(sourcePath), StandardCharsets.UTF_8);
             BufferedWriter writer = Files.newBufferedWriter(Paths.get(targerPath), StandardCharsets.UTF_8)) {

            String line;
            while ((line = reader.readLine()) != null) {
                writer.write(line);
                writer.newLine();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>


## 1-1. Java 6 이하 버전 파일 Reader/Writer 예제 코드

Java 6 이하에서 파일을 처리하던 방식으로 Java I/O를 활용한 전통적인 방식의 코드이다.

```java
public class OldStyleBufferedFileExample {
    public static void main(String[] args) {
        String sourcePath = "/Users/example/file_test/file1.txt";  // 입력 파일 경로
        String targerPath = "/Users/example/file_test/file1_copy.txt"; // 출력 파일 경로

        BufferedReader reader = null;
        BufferedWriter writer = null;
        try {
            reader = new BufferedReader(new FileReader(sourcePath));
            writer = new BufferedWriter(new FileWriter(targerPath));

            String line;
            while ((line = reader.readLine()) != null) {
                writer.write(line);
                writer.newLine();
            }
        } catch (IOException e) {
            e.printStackTrace();
            
        } finally {
            try {
                if (reader != null) {
                    reader.close();
                }
                if (writer != null) {
                    writer.close();
                }
            } catch (IOException ex) {
                ex.printStackTrace();
            }
        }
    }
}
```

<br>


## 1-2. `try-with-resources 구문`

Java 7에서 도입된 try-with-resources 구문은 자동 자원 관리(Automatic Resource Management, ARM)를 지원하여 파일 I/O와 같이 자원을 사용하는 코드를 작성할 때 finally 블록에서 명시적으로 자원을 닫을 필요를 없애주는 기능으로 이 구문을 사용하면, 자원을 사용하는 클래스가 AutoCloseable 인터페이스를 구현하고 있다면 해당 자원이 try 블록을 벗어날 때 자동으로 close() 메서드가 호출되어 자원이 안전하게 닫힌다.

### NIO 파일 Read/Write 예제 코드

```java
try {
    reader = new BufferedReader(new FileReader(inputFilePath));
    writer = new BufferedWriter(new FileWriter(outputFilePath));
    //...
```

### Java 6 이하 버전 파일 Read/Write 예제 코드

```java 
try (BufferedReader reader = Files.newBufferedReader(Paths.get(inputFilePath), StandardCharsets.UTF_8);
    BufferedWriter writer = Files.newBufferedWriter(Paths.get(outputFilePath), StandardCharsets.UTF_8)) {
    //...
```

<br>

# 2. File NIO newStream Reader/Writer

Input/Output Stream 방식은 Reader/Writer 보다 먼저 자바에서 채택 된 입출력 방식이다. 파일뿐만 아니라 네트워크 등 데이터 통신 등에서 사용된다. File NIO에서도 입출력 스트림을 제공하고 있다. 아래 코드들은 Stream 기반으로 Files 클래스의 newInputStream와 newOutputStream 를 사용한 예제코드이다.

<br>

## 2-1. Stream 기반의 BufferedReader 와 BufferedWriter 텍스트 파일 방식

```java
public class NIOStreamFileExample {

    public static void main(String[] args) {
        String sourcePath = "/Users/example/file_test/file2.txt";  // 입력 파일 경로
        String targetPath = "/Users/example/file_test/file2_copy.txt"; // 출력 파일 경로

        try (InputStream is = Files.newInputStream(Paths.get(sourcePath));
             BufferedReader reader = new BufferedReader(new InputStreamReader(is));
             OutputStream os = Files.newOutputStream(Paths.get(targetPath));
             BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(os))
        ) {
            String line;

            //파일에서 한 줄씩 데이터를 읽어온다
            while ((line = reader.readLine()) != null)  {
                writer.write(line);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

<br>


## 2-2. Stream 기반의 스트림 BufferedInputStream 와 BufferedOutputStream 바이트 데이터 방식

```java
public class NIOBufferedStreamFileExample {

    public static void main(String[] args) {
        String sourcePath = "/Users/example/file_test/file2.txt";  // 입력 파일 경로
        String targetPath = "/Users/example/file_test/file2_copy.txt"; // 출력 파일 경로

        try (InputStream is = Files.newInputStream(Paths.get(sourcePath));
            BufferedInputStream bis = new BufferedInputStream(is);
            OutputStream os = Files.newOutputStream(Paths.get(targetPath));
            BufferedOutputStream bos = new BufferedOutputStream(os)
        ) {

            String line;
            int i = 0;
            byte[] buffer = new byte[1024];

            //파일에서 일정 크기의 버퍼만큼 데이터를 읽어온다
            while ((i = bis.read(buffer)) != -1)  {
                bos.write(buffer, 0, i);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br><br>


---