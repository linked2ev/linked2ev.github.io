---
layout: post
title:  "[Java] FILE (6) ByteArrayInputStream 과 ByteArrayOutputStream 바이트 배열 스트림"
subtitle:   "[Java]"
categories: java
tags: java-ch-file
comments: true
---

ByteArrayInputStream 과 ByteArrayOutputStream 은 Buffer 을 이용한 방식이 아닌 바이트배열에 데이터를 입출력하는데 사용되는 스트림이다.


<br><br>


# 1. ByteArrayInputStream 과 ByteArrayOutputStream

ByteArrayInputStream 과 ByteArrayOutputStream 은 메모리, 즉 `바이트배열에 데이터를 입출력하는데 사용되는 스트림`이다.  
`주로 다른 곳에 입출력하기 전에 데이터를 임시로 바이트 배열에 담아서 변환 등의 작업` 시에 사용 된다.

ByteArrayInputStream은 바이트 배열을 차례대로 읽어 들이기 위한 클래스이며, ByteArrayOutputStream은 내부적으로 저장 공간에 쌓이고 toByteArray() 로 저장된 모든 내용이 바이트 배열로 반환 된다.

<br>

바이트 배열(byte[])은 파일 데이터 및 이진 데이터을 저장하고 새로운 데이터를 다시 저장 및 확장하는 등 byte[]를 동적으로 편리하게 쓰기 위함이며, 파일 내용 및 데이터를 메모리에 로드해야 한다는 것입니다.

byte[]의 경우 금방쓰고 버리고 하는 사용 빈도수가 높기때문에 GC(garbage collection)이 자주 작동하게 되고 성능에 영향을 준다.
그래서 효율적으로 사용하기 위해 나온것이 ByteBuffer와 ByteArrayOutputStream이다. ByteArrayOutputStream은 저장소 개념으로 사용되며, 정확한 길이를 모를때 효율적이다.
    
- `c.f) JVM이 다루는 Heap 메모리보다 큰 배열을 사용할 수 없음`
   
<br>


## ByteArrayOutputStream 이용한 File 데이터 반환

<br>

```java
/**
 * 1. ByteArrayOutputStream 을 이용한 File 을 read 해서 데이터 반환하는 메서드
 * @param fileMap
 * @param filePath
 * @param fileName
 */
public static void readFileBaosToData (Map<String, Object> fileMap, String filePath, String fileName) {

	String fileFullPath = filePath + fileName;
	FileInputStream fis = null;
	ByteArrayOutputStream baos = null;

	try {
		fis  = new FileInputStream(fileFullPath);
		baos = new ByteArrayOutputStream();

		byte[] buf  = new byte[1024];
		int readLen = buf.length;
		int bytesRead = 0;

	    //1. 파일로부터 읽어들인 바이트 배열 수(readLen) 만큼을 buf 에 저장
		while((bytesRead = fis.read(buf, 0, readLen))!=-1) {//-1이 아닐때까지 읽어
			//출력한 결과(buf)를 ByteArrayOutputStream 의 내부저장 공간에 쌓음(baos)
			baos.write(buf, 0, bytesRead);
		}

		//2. ByteArrayOutputStream 의 내부 저장공간에 저장된 바이트 배열(파일 내용)을 전부 반환
		byte[] fileByteArray = baos.toByteArray();
		System.out.println("파일의 내용(데이터)를 모두 읽어들여 바이트 배열로 저장");
		System.out.println("읽어들인 byte 수 :" + fileByteArray.length);

		fileMap.put("retData", new String(fileByteArray) );

	} catch (Exception e) {
		e.printStackTrace();
	} finally {
		try {
			if (baos != null) baos.close();
		} catch (Exception e) {
		}
	}
}
```

<br><br>

작성중 .. 다른거 하는중.

---
