---
layout: post
title:  "[Java] FILE 입출력(I.O) (2) - Byte Stream(바이트기반 스트림)"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

바이트 기반 스트림, 바이트 보조 스트림과 FILE 입출력(I/O)에 대해 정리 (2)

<br>


# 1 바이트 기반 스트림

# 1-1 InputStream과 OutputStream

InputStream과 OutputStream은 모든 바이트기반의 스트림의 조상

<br>

## 1-1-1 InputStream

`InputStream은 바이트 기반 입력 스트림의 최상위 클래스(조상)로 추상 클래스입니다. 모든 바이트 기반 입력 스트림은 이 클래스를 상속`

<br>

정의 된 메서드 | 설명
--- | ---
`int available()` | - `스트림으로부터 현재 읽을 수 있는 바이트 수를 얻음`
`void close()` | - inputStream 을 닫음<br>- `스트림을 닫음으로써 사용하고 있던 자원을 반환`
void mark(int readlimit) | - inputStream 에서 현재 위치 표시
abstract int read() | - inputStream 에서 한 바이트를 읽어서 int 값 반환<br>- `1byte를 읽어온다.(0~255 사이의 값), 더 이상 읽어 올 데이터가 없으면 -1을 반환`
`int read(byte[] b)` | - `byte[] 배열 b 만큼의 데이터를 읽어서 b에 저장하고 읽은 바이트 수를 반환`
`int read(byte[] b, int off, int len)` | - `최대 len개의 byte를 읽어서, byte[] 배열 b의 지정된 위치(off)부터 저장하고 읽은 바이트 수 반환`
void reset() | - mark() 마지막으로 호출한 위치로 이동
long skip(long n) | - inputStream 에서 n 바이트 만큼 데이터를 스킵

<br><br>


## 1-1-2 OutputStream

`OutputStream은 바이트 기반 출력 스트림의 최상위 클래스로 추상클래스(조상)입니다. 모든 바이트 기반 출력 스트림 클래스는 이 클래스를 상속`

<br>

정의 된 메서드 | 설명
--- | ---
`void close()` | - 입력소스를 닫음, 사용하고 있던 자원 반환
`void flush()` | - `스트림버퍼에 남은 모든 내용을 출력소스에 쓴다.`
abstract void write(int b) | - 주어진 값을 출력소스에 쓴다.
`void write(byte[] b)` | - `주어진 배열 b에 저장된 모든 내용을 출력소스에 쓴다.`
`void write(byte[] b, int off, int len)` | - `주어진 배열 b에 저장된 내용 중에서 off번째부터 len개 만큼만을 읽어서 출력소스에 쓴다.`

<br><br>


# 2 FileInputStream과 FileOutputStream

FileInputStream과 FileOutputStreamdms 파일에 입출력을 하기 위한 스트림

<br>

## 2-1 FileInputStream

FileInputStream 생성장 | 설명
--- | ---
FileInputStream(String name) | - 지정 된 파일이름을 가진 실제 파일과 연결 된 FileInputStream을 생성
FileInputStream(File file) | - File 인스턴스로 지정한 파일에 FileInputStream을 생성

<br><br>


## 2-2 FileOutputStream

FileOutputStream 생성자 | 설명
--- | ---
FileOutputStream(String name) | 지정 된 파일이름을 가진 실제 파일과 연결 된 FileOutputStream을 생성
FileOutputStream(String name, boolean append) | - 지정 된 파일이름을 가진 실제 파일과 연결 된 FileOutputStream을 생성<br>- 두번째 인자 append는 true는 내용 덧붙이기, false는 기존 파일 덮어쓰기
FileOutputStream(File file) | - File 인스턴스로 지정한 파일에 FileOutputStream을 생성

<br><br>


## ◼︎ FileInputStream과 FileOutputStream을 사용한 파일 복사하는 예제

<br>

```java
import java.io.*;

class FileCopy {
	public static void main(String args[]) {
		try {
			FileInputStream fis = new FileInputStream(args[0]);
			FileOutputStream fos = new FileOutputStream(args[1]);

			int data =0;
			while((data=fis.read())!=-1) {
				fos.write(data);	 // void write(int b)
			}

			fis.close();
			fos.close();
		} catch (IOException e) {
			e.printStackTrace();		
		}
	}
}
```

<br>


> `read()의 반환값이 int형(4byte)이긴 하지만, 더 이상 입력값이 없음을 알리는 -1을 제외하고는 0~255(1byte)의 범위의 정수값이기 때문에, char형(2byte)으로 변환한다해도 손실되는 값이 없다.`<br>read()가 한 번에 1byte씩 파일로부터 데이터를 읽어 들이긴 하지만, 데이터의 범위가 십진수로 0~255(16진수로는 0x00~0xff)범위의 정수값이고, 또 읽을 수 있는 입력값이 더 이상 없음을 알릴 수 있는 값도 필요하다.<br>그래서 다소 크긴 하지만 정수형 중에서는 연산이 가장 효율적이고 빠른 int형 값을 반환하도록 한 것이다.

<br><br>


## ◼︎ FileOutputStream을 사용한 파일 쓰기 예제

```java
import java.io.*;

class FileReaderEx1 {

	public static void main(String args[]) {
		  try {
            File f = new File("write_test.txt");
            FileOutputStream fos = new FileOutputStream(f, true);

            //Java
            byte[] by = new byte[]{'H', 'e', 'l', 'l', 'o', ' ', 'J', 'a', 'v', 'a', '>'};
            fos.write(by, 6,5);

            //FileOutputStream 추가 내용 쓰기
			String contents = "File 추가 내용 데이터"
            fos.write(contents.getBytes());
            fos.close();

        } catch (IOException e) {
            e.printStackTrace();
        }
	}
}
```

> 실행결과

```
Java>File추가 내용 데이터
```

<br><br>



# 2 바이트기반의 보조스트림

<br>

# 2-1 FilterInputStream과 FilterOutputStream

FilterInputStream과 FilterOutputStream은 `InputStream/OutputStream의 자손이면서 모든 보조 스트림의 조상`이며,
보조 스트림은 자체적으로 입출력을 수행할 수 없기 때문에 기반 스트림을 필요로 한다.

<br>

> FilterInputStream과 FilterOutputStream의 생성자

```
Protected FilterInputStream(InputStream in)
public FilterOutputStream(OutputStream out)
```

- FilterInputStream과 FilterOutputStream의 모든 메서드는 단순히 기반 스트림의 메서드를 그대로 호출
- FilterInputStream과 FilterOutputStream 자체로는 아무런 일도 하지 않음을 의미
- 상속을 통해 원하는 작업을 수행하도록 읽고 쓰는 메서드를 오버라이딩으로 구현

<br><br>


# 2-2 BufferedInputStream과 BufferedOutputStream

`한 바이트씩 입출력하는 것 보다 버퍼(바이트배열)를 사용해서 한 번에 여러 바이트를 입출력하는 것이 빠르기 때문에 대부분의 입출력 작업에 사용`되는 버퍼를 이용한 보조 스트림이다.

<br>

## 2-2-1. BufferedInputStream 

<br>

메서드/생성자 | 설명
--- | ---
`BufferedInputStream(inputStream in, int size)` | - 주어진 InputStream 인스턴스를 입력소스(Input source)로 지정된 크기(byte단위)의 버퍼를 갖는 BufferedInputStream 인스턴스 생성 
`BufferedInputStream(inputStream in)` | - 주어진 InputStream 인스턴스를 입력소스(Input source)로 지정된 크기(byte단위)의 버퍼를 갖는 BufferedInput Stream 인스턴스 생성<br>- 기본적으로 8192 byte크기의 버퍼를 갖는다.

<br>

## `◼︎ BufferedInputStream 처리`

1 ) 프로그램에서 입력 소스로부터 데이터를 읽기 위해 처음으로 read 메서드를 호출  
2 ) `BufferedInputStream은 입력 소스로 부터 버퍼 크기만큼의 데이터를 읽어다 자신의 내부 버퍼에 저장`  
3 ) `프로그램에서는 BufferedInputStream의 버퍼에 저장된 데이터를 읽음`, 외부의 입력 소스로 부터 읽는 것보다 내부의 버퍼로 읽는 것이 훨씬 빠르기 때문에 그만큼 작업 효율이 높아진다.  
4 ) 프로그램에서 버퍼에 저장된 모든 데이터를 다 읽고 그 다음 데이터를 읽기 위해 read 메서드가 호출되면, BufferedInputStream은 입력 소스로부터 다시 버퍼 크기만큼의 데이터를 읽어다 버퍼에 저장해 놓는다.


`프로그램에서 직접 데이터를 보내는것이 아닌 내부 메모리 버퍼에 쌓아두었다가 버퍼 안에 있는 데이터를 한번에 전송하기 떄문에 성능 향상 되는 개념`이다.

<br>

>`BufferedInputStream과 BufferedOutputStream의 버퍼크기는 입력소스로부터 한 번에 가져올 수 있는 데이터의 크기로 지정하면 좋다.` 보통 입력소스가 파일인 경우 보통 작게는 1024부터 2048 또는 4096 정도의 크기로 하는 것이 보통이며, 버퍼의 크기를 변경해가면서 테스트하면 최적의 버퍼 크기를 알아낼 수 있다.

<br><br>



## 2-2-2. BufferedOutputStream

<br>

메서드/생성자 | 설명
--- | ---
`BufferedOutputStream(OutputStream out, int size)` | - 주어진 OutputStream 인스턴스를 출력소스(output source)로하며 지정 된 크기(byte)의 버퍼를 갖는 BufferedOutputStream 인스턴스를 생성
`BufferedOutputStream(OutputStream out)` | - 주어진 OutputStream 인스턴스를 출력소스(output source)로하며 지정 된 크기(byte)의 버퍼를 갖는 BufferedOutputStream 인스턴스를 생성<br>- 기본적으로 8192 byte크기의 버퍼를 갖는다.
`flush()` | - 버퍼의 모든 내용을 출력소스에 출력하고, 버퍼를 비움
`close()` | - 버퍼의 모든 내용을 출력소스에 출력하고, BufferedOutputStream 인스턴스가 사용하던 모든 자원 반환

<br>

## ◼︎ `BufferedOutputStream 처리`

1 ) 프로그램에서 write 메서드를 이용한 출력이 BufferedOutputStream의 버퍼에 저장  
2 ) `버퍼가 가득 찰 경우 버퍼의 모든 내용을 출력 소스에 출력`


`입력 소스로부터 자신의 내부 버퍼 크기만큼 데이터를 미리 읽고 버퍼에 저장한 후에, 프로그램은 버퍼가 대신 읽음으로써 성능이 향상되는 개념`이다.

<br>

> 버퍼가 가득 찼을때만 출력소스에 출력을 하기 때문에, 마지막 출력 부분이 출력소스에 쓰여지지 못하고, BufferedOutputStream의 버퍼에 남아있는 채로 프로그램이 종료될 수 있다는 점을 주의해야 한다. `그래서 프로그램에서 모든 출력 작업을 마친 후 BufferedOutputStream에 close()나 flush()를 호출해서 마지막에 버퍼에 있는 모든 내용이 출력소스에 출력되도록 해야 한다.`

<br><br>


## ◼︎ `BufferedOutputStream 설명 예제`

```java
import java.io.*;

class BufferedOutputStreamEx1 {
	public static void main(String args[]) {
		try {
		    FileOutputStream fos = new FileOutputStream("123.txt");	
	        BufferedOutputStream bos = new BufferedOutputStream(fos, 5);
		    // BufferedOutputStream의 버퍼 크기를 5로 한다.
			   
		    for(int i=1; i <= 9; i++) {   
			    bos.write(i);
                // 파일 123.txt에  1 부터 9까지 출력한다.
		     }
		    fos.close();
		} catch (IOException e) {
		    e.printStackTrace();		
		}
	}
}
```

<br>

크기가 5인 BufferedOutputStream을 이용해서 파일 123.txt에 1부터 9까지 출력하는 예제인데 결과는 5까지만 출력된다. 그 이유는 버퍼에 남아있는 데이터가 출력되지 못한 상태로 프로그램이 종료되었기 때문이다.

이 예제에서 fos.close()를 호출해서 스트림을 닫아주기는 했지만, 이렇게 해서는 BufferedOutputStream의 버퍼에 있는 내용이 출력되지 않는다. bos.close();와 같이 해서 BufferedOutputStream의 close()를 호출해 주어야 버퍼에 남아있던 모든 내용이 출력된다.

BufferedOutputStream의 close()는 기반 스트림인 FileOutputStream의 close()를 호출하기 때문에 FileOutputStream의 close()는 따로 호출해주지 않아도 된다.
보조스크림을 사용한 경우에는 기반스트림의 close()나 flush()를 호출할 필요없이 단순히 보조스트림의 close()를 호출하기만 하면 된다.

<br>

---

[참고]  

- 자바의 정석 2판 - 바이트 기반 스트림