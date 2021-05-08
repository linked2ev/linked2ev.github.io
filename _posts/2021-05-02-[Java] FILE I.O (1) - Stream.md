---
layout: post
title:  "[Java] FILE 입출력(I.O) (1) - Stream(스트림)"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

자바에서 Stream 과 FILE 입출력(I/O)에 대해 정리 (1) 포스팅이다.

<br>


# 1.1 자바에서 입출력이란?

I/O란 Input과 Output 의 약자로 입력과 출력을 말하며, 입출력은 컴퓨터 내부 또는 외부 장치들 간에 데이터를 주고 받는 것을 말한다.

<br><br>


# 1-2 스트림(Stream)이란?

자바에서 입출력을 수행하려면, 즉 어느 한쪽에서 다른 쪽으로 데이터를 전달하려면, 두 대상을 연결하고 데이터를 전송할 수 있는 무언가가 필요한데 이것을 스트림이라 한다.
즉, `스트림이란 데이터를 운반하는 통로이다.`

- 스트림은 단방향 통신만 가능하기 때문에 하나의 스트림으로 입력과 출력을 동시에 처리할 수 없다.
- 동시에 수행하려면 입력스트림과 출력스트림 모두 필요하다.
- 큐(queue)와 같은 FIFO 구조로 되어 있다.

<br>

그리고 바이너리 형태로 데이터를 입출력하는 스트림을 `이진스트림`, 문자형태로 입출력하는 스트림을 `텍스트스트림`이라고 합니다.

<br><br>


# 2-1 바이트기반 스트림 - InputStream, OutputStream

`바이트 단위`로 데이터를 전송하는 스트림이다. 입출력의 단위가 1byte라는 뜻이다.
`최상위 클래스는 InputStream과 OutputStream`이다. FILE IO에서는 FileInputStream, ByteArrayInputStream 등이 상속하고 있다.

<br> 

스트림은 바이트단위로 데이터를 전송하며 입출력 대상에 따라 다음과 같은 입출력 스트림이 있다.
스트림의 종류가 달라도 읽고 쓰는 방법은 동일하다.


입력스트림 | 출력스트림 | 입출력 대상의 종류
--- | --- | ---
FileInputStream | FileOutputStream | 파일 
ByteArrayInputStream | ByteArrayOutputStream | 메모리(byte 배열) 

<br><br>


# 2-2 InputStream 과 OutputStream 메서드

> 바이트 기반 스트림 읽기와 쓰기 메서드

InputStream | OutputStream
--- | ---
abstract int read() | abstract void write(int c) 
int read(byte cbuf[]) | void write(byte cbuf[]) 
int read(byte cbuf[], int offset, int length) | void write(byte cbuf[], int offset, int length) 

<br>

- read(byte[] b): 배열 b의 크기만큼 데이터를 읽어와서 b에 저장한다.
- read(byte[] b, int off, int len) : len의 크기만큼 데이터를 읽어와서 배열 b의 off 위치부터 저장한다.
- write(byte[] b) : 배열 b에 저장된 모든 내용을 출력소스에 쓴다
- write(byte[] b, int off, int len) : 배열 b에 저장된 내용을 off 위치부터 len개 만큼 출력소스에 쓴다.

<br><br>


# 3-1 문자기반 스트림 - Reader, Writer

Java에서는 한 문자를 의미하는 char형이 1byte가 아니라 2 byte이기 때문에 바이트기반의 스트림으로 2byte인 문자를 처리하는데 어려움이 있다.

이 점을 보완하기 위해서 문자기반의 스트림이 제공하기에 `문자데이터를 입출력할 때는 바이트기반 스트림 대신 문자기반 스트림을 사용`하자.

<br>

> InputStream -> `Reader`<br>
OutputStream -> `Write`

<br>

바이트 기반 스트림 | 문자 기반 스트림
--- | ---
FileInputStream<br>FileOutStream | FileReader<br>FileWriter
ByteArrayInputStream<br>ByteArrayOutputStream | CharArrayReader<br>CharArrayWriter
~~StringBufferInputStream<br>StringBufferOutputStream~~ | StringReader<br>StringWriter

<br><br>


# 3-2 Reader 와 Writer 메서드

> 문자 기반 스트림 읽기와 쓰기 메서드

Reader | Writer
--- | ---
int read()<br>int read(char[] cbuf)<br>abstract int read(char[] cbuf, int off, int len) |void write(int c)<br>void write(char[] cbuf)<br>abstract void write(char cbuf[], int off, int len)<br>void write(String str)<br>void write(String str, int off, int len)

<br><br>


# 4-1 보조 스트림 - Buffer

스트림의 기능을 보완하기 위한 보조 스트림이 제공된다. 보조 스트림은 실제 데이터를 주고받는 스트림이 아니기 때문에 데이터를 입출력할 수 있는 기능을 없지만. `스트림의 기능을 향상시키거나 새로운 기능을 추가`할 수 있다.그래서 보조 스트림만으로는 입출력 처리를 할 수 없고, 스트림을 먼저 생성한 다음에 이를 이용해서 보조 스트림을 생성해야 한다.

예를 들어 test.txt라는 파일을 읽기 위해 FileInputStream을 사용할 때, 입력 성능을 향상시키기 위해 버퍼를 사용하는 보조 스트림인 BufferedInputStream을 사용하는 코드는 다음과 같다.

<br>

```java
// 먼저 기반스트림을 생성한다. FileInputStream은 InputStream의 자손 클래스이다.
FileInputStream fis = new FileInputStream("test.txt");

// 기반스트림을 이용해서 보조스트림을 생성한다.
BufferedInputStream bis = new BufferedInputStream(fis);

// 보조 스트림인 BufferedInputStream으로 부터 데이터를 읽는다.
bis.read();
```

- 실제 입력기능은 FileInputStream이 수행
- 보조스트림인 BufferedinputStream 은 버퍼만을 제공

<br>

> 보조스트림 종류

바이트기반 보조스트림 | 문자기반 보조스트림
--- | ---
BuffererdInputStream<br>BuffererdOutputStream | BuffererdReader<br>BuffererdWriter
FilterInputStream<br>FilterOutputStream | FilterReader<br>FilterWriter

<br><br>


---

[참고]  

- 자바의 정석 2판