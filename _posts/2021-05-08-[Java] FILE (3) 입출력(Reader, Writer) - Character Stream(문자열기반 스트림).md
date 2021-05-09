---
layout: post
title:  "[Java] FILE (3) 입출력(Reader/Writer) - Character Stream(문자열기반 스트림)"
subtitle:   "[Java]"
categories: java
tags: java-ch-file
comments: true
---

문자열 기반 스트림, 문자열 보조 스트림과 FILE 입출력(Reader/Writer)에 대해 정리 (3)

<br>


# 1 문자 기반 스트림

문자데이터를 다루는 데 사용된다는 것을 제외하고는 바이트기반 스트림과 문자기반 스트림의 사용방법은 거의 같다.

<br>


# 1-1 Reader와 Writer

Reader와 Writer는 모든 문자기반의 스트림의 조상이며, Reader/Writer 메서드는 byte 배열이 아닌 char 배열을 사용한다.  
문자 기반 스트림이 단순히 2byte로 스트림을 처리한다는 것만을 의미하지 않으며, Reader/Writer는 여러 종류 인코딩과 자바에서 사용하는 유니코드간의 변환을 자동으로 처리해준다.

<br>

## 1-1-1 Reader

`Reader는 문자 기반 입력 스트림의 최상위 클래스(조상)로 추상 클래스입니다. 모든 문자 기반 입력 스트림은 이 클래스를 상속`

<br>

정의 된 메서드 | 설명
--- | ---
`abstract void close()` | - inputStream 을 닫음<br>- `스트림을 닫음으로써 사용하고 있던 자원을 반환`
void mark(int readlimit) | - inputStream 에서 현재 위치 표시
int read() | - 입력소스로부터 하나의 문자를 읽어서 0~65535 범위의 정수값을 반환, 더 이상 읽어 올 데이터가 없으면 -1을 반환
`int read(char[] c)` | - `입력소스로부터 매개변수로 주어진 배열 c의 크기만큼 읽어서 배열 c에 저장, 읽어 온 데이터의 개수 또는 -1을 반환`
`abstract int read(char[] c, int off, int len)` | - `입력소스로부터 최대 len개의 byte를 읽어서, char[] 배열 c의 지정된 위치(off)부터 저장하고 읽은 데이터 수 또는 -1 반환`
boolean ready() | 입력소스로부터 데이터를 읽을 준비가 되어 있는 알려줌
void reset() | - mark() 마지막으로 호출한 위치로 이동
long skip(long n) | - 현재위치에서 문자 수(n) 만큼 스킵

<br><br>


## 1-1-2 Writer

`Writer는 문자 기반 출력 스트림의 최상위 클래스로 추상클래스(조상)입니다. 모든 문자 기반 출력 스트림 클래스는 이 클래스를 상속`

<br>

정의 된 메서드 | 설명
--- | ---
`void close()` | - OutputStream 을 닫음, 사용하고 있던 자원 반환
`void flush()` | - `스트림버퍼에 남은 모든 내용을 출력소스에 쓴다.`
void write(int i) | - 주어진 값을 출력소스에 쓴다.
`void write(char[] c)` | - `주어진 char[] 배열의 모든 내용을 출력소스에 쓴다.`
`void write(char[] c, int off, int len)` | - `주어진 char c에 저장된 내용 중에서 off번째부터 len개 만큼만을 읽어서 출력소스에 쓴다.`

<br><br>



# 2 FileReader와 FileWriter

FileReader/FileWriter 파일로부터 텍스트 데이터를 읽고 쓰는데 사용하머, FileInputStream/FileOutputStream 사용방법은 비슷하다.

<br>

## 2-1 FileReader 와 FileInputStream 비교 설명

<br>

```java
import java.io.*;

class FileReaderEx1 {

	public static void main(String args[]) {

		try {
			
			String fileName = "test.txt";
			FileInputStream fis = new FileInputStream(fileName);
			FileReader fr = new FileReader(fileName);
			
			int data =0;

			// FileInputStream을 이용해서 파일내용을 읽어 화면에 출력한
			// 1 byte 출력 -> 한글깨짐
			while((data=fis.read())!=-1) {
				System.out.print((char)data);
			}
			System.out.println();
			fis.close();

			// FileReader를 이용해서 파일내용을 읽어 화면에 출력한
			// 2 byte 출력
			while((data=fr.read())!=-1) {
				System.out.print((char)data);
			}
			System.out.println();
			fr.close();				

		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

> 실행결과

```
//test.txt 내용 :  Hello. 문자열 스트림 안녕~
Hello. ë¬¸ìì´ ì¤í¸ë¦¼ ìë~
Hello. 문자열 스트림 안녕~
```

<br><br>

## ◼︎ FileInputStream과 FileOutputStream을 사용한 파일 복사하는 예제

<br>

```java
import java.io.*;

class FileCopy {

	public static void main(String args[]) {
		try {
			FileReader fr = new FileReader(args[0]);
			FileWriter fw = new FileWriter(args[1]);

			int data =0;
			while((data=fr.read())!=-1) {
				fr.write(data);	 // void write(int b)
			}

			fr.close();
			fw.close();
		} catch (IOException e) {
			e.printStackTrace();		
		}
	}
}
```

<br><br>


# 2 문자기반 보조 스트림

<br>

# 2-1 BuffredReader와 BufferedWriter

BuffredReader와 BufferedWriter는 버퍼를 이용해서 입출력의 효율을 높일 수 있도록 해주는 역할을 한다.
- BuffredReader의 readLine()는 데이터를 라인단위로 읽어올 수 있는 메서드
- BufferedWriter의 newLine()는 줄바꿈 해주는 메서드

<br>

## ◼︎ FileReader와 BufferedReader를 사용해 파일 읽는 예제

<br>

```java
import java.io.*;

class BufferedReaderEx1 {

	public static void main(String[] args) {
        
		try {
            FileReader fr = new FileReader("test2.txt");
            BufferedReader br = new BufferedReader(fr);

            String line = "";

			//while((data=fr.read())!=-1) 과 동일하다
			//br.readLine() 을 통해 line 변수에 데이터를 넣고 null 아니면 for문 실핼
            for (int i=1; (line = br.readLine()) != null; i++) {
                System.out.println(line);
            }
            br.close();

        } catch(IOException e) {}

	}
}
```

> 실행결과

```
Hello. 문자열 스트림

TEST2
BuffredReader와 BufferedWriter 예제
출력끝
```

<br><br>


# 2-2 InputStreamReader와 OutputStreamWriter

바이트 기반 스트림을 문자 기반 스트림으로 연결시켜주는 역할을 한다. 그리고 바이트기반 스트림의 데이터를 지정된 인코드의 문자 데이터로 변환하는 작업을 수행한다.

<br>

## 2-2-1 InputStreamReader

생성자 / 메섣드 | 설명
--- | ---
inputStreamReader(InputStream in) | OS의 기본 인코딩의 문자로 변환하는 inputStreamReader 생성
inputStreamReader(InputStream in, String ecoding) | 지정 된 인코딩으로 inputStreamReader 생성
String getEncoding() | inputStreamReader의 인코딩을 알려준다.

<br>


## 2-2-2 OutputStreamWriter

생성자 / 메섣드 | 설명
--- | ---
OutputStreamWriter(OutputStream in) | OS의 기본 인코딩의 문자로 변환하는 OutputStreamWriter 생성
OutputStreamWriter(OutputStream in, String ecoding) | 지정 된 인코딩으로 OutputStreamWriter 생성
String getEncoding() | OutputStreamWriter의 인코딩을 알려준다.

<br><br>


## ◼︎ InputStreamReader와 BufferedReader를 이용한 출력 예제

<br>

```java
import java.io.*;

class InputStreamReaderEx {

	public static void main(String[] args) {
		
		try {
        	//System.in은 InputStream의 객체
            InputStream in = System.in;
            //int byte = System.in.read();
            InputStreamReader isr = new InputStreamReader(in);
            BufferedReader br = new BufferedReader(isr);

            System.out.println("사용중인 OS의 인코딩 :" + isr.getEncoding());

            String line = "";
            do {
                System.out.print("문장을 입력하세요. 마치시려면 q를 입력하세요. ");
                line = br.readLine();
                System.out.println("입력하신 문장 : "+line);

            } while(!line.equalsIgnoreCase("q"));

            System.out.println("프로그램을 종료합니다.");

        } catch(IOException e) {}
	}
}

```

> 실행결과

```
사용중인 OS의 인코딩 :UTF8
문장을 입력하세요. 마치시려면 q를 입력하세요. 안녕하세요 문장 입력 
입력하신 문장 : 안녕하세요 문장 입력 
문장을 입력하세요. 마치시려면 q를 입력하세요. 
```

<br>

---

[참고]  

- 자바의 정석 2판 - 문자 기반 스트림