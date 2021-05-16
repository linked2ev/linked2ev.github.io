---
layout: post
title:  "[Java] FILE (5) 파일 입출력(InputStream, OutStream, Reader, Writer) 예제 코드"
subtitle:   "[Java]"
categories: java
tags: java-ch-file
comments: true
---

바이트 (Byte) - Input/Output(I/O) 와 문자 (Charater) - Reader/Writer 기본 예제 및 파일 데이터 반환 및 파일 복사 예제 설명

<br>



# Stream(스트림)

데이터를 어떤 방식으로 전달하느냐에 따라 스트림은 두가지

<br>

## 바이트 (Byte) - Input/Output(I/O)

- InputStream -> FileInputStream -> BufferedInputStream (입력)
- OutStream -> FileOutputStream -> BufferedOutputStream (출력)

## 문자 (Charater) - Reader/Writer

- Reader -> FileReader -> BufferedReader (입력)
- Writer -> FileWriter -> BufferedWriter (출력)

<br><br>


# 1. FileWriter / FileReader 예제

<br>

```java
/**
 * 1-1. FileWriter 클래스
 * - 문자 기반 출력 스트림 최상위 클래스인 Writer 를 상속
 * @param fileData
 * @param filePath
 * @param fileName
 */
public static void fileWriteToFile (String fileData, String filePath, String fileName) {
    String d = fileData;
    isMkdirs(filePath);

    File file = new File(filePath + fileName);
    FileWriter fw = null;

    try {
        // 기존 파일의 내용에 이어서 쓰려면 true, 새로 덮어 쓸땐 false 지정한다.
        fw = new FileWriter(file, false);
        fw.write(d);
        fw.flush();

        System.out.println("FileWrite File Created");

    } catch(IOException e) {
        e.printStackTrace();

    } finally {
        try {
            if (fw != null) fw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

/**
 * 1-2. FileRead 클래스
 * - 문자 기반 입력 스트림 최상위 클래스인 Reader 를 상속
 * @param fileMap
 * @param filePath
 * @param fileName
 */
public static void fileReader (Map<String, Object> fileMap, String filePath, String fileName) {
    String fileFullPath = filePath + fileName;
    FileReader fr = null;

    try {
        //파일 객체 생성
        File file = new File(fileFullPath);
        //입력 스트림 생성
        fr = new FileReader(file);

        int size = isFileSize(file);
        char[] cuf = new char[ size ];

        //입력소스로부터 최대 len 개의 byte 수를 읽어서, 
        //char[] 배열 c의 지정된 위치(off)부터 저장하고 읽은 데이터 수 또는 -1 반환
        fr.read(cuf, 0, size);
        System.out.println("FileReader Read");
        fileMap.put("retData", String.valueOf(cuf) );

    } catch(IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fr != null) fr.close();
        } catch(IOException e) {
            e.printStackTrace();
        }
    }
}


```

<br><br>


# 2. BufferedWriter / BufferedReader 예제

<br>

```java
/**
 * 2-1. FileWriter 에 BufferedWriter 적용
 * - Buffer 을 적용 입력의 효율을 높임
 * @param fileData
 * @param filePath
 * @param fileName
 */
public static void fileWriteBufferToFile (String fileData, String filePath, String fileName) {
    String d = fileData;
    isMkdirs(filePath);

    File file = new File(filePath + fileName);
    FileWriter fw = null;
    BufferedWriter bfw = null;

    try {
        fw = new FileWriter(file, false);
        bfw = new BufferedWriter(fw);

        //FileWriter write()와 flush() 메소드가 BufferedWriter 인스턴스를 이용
        bfw.write(d);
        bfw.flush();

        System.out.println("BufferedWriter File Created");

    } catch(IOException e) {
        e.printStackTrace();

    } finally {
        try {
            if (bfw != null) bfw.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

/**
 * 2-2. FileReader 에 BufferedReader 적용
 * - Buffer 을 적용 출력의 효율을 높임
 * @param fileMap
 * @param filePath
 * @param fileName
 */
public static void fileReaderBuffer (Map<String, Object> fileMap, String filePath, String fileName) {
    String fileFullPath = filePath + fileName;
    FileReader fr = null;
    BufferedReader bfr = null;

    try {
        //파일 객체 생성
        File file = new File(fileFullPath);
        //입력 스트림 생성
        fr = new FileReader(file);
        bfr = new BufferedReader(fr);

        int size = isFileSize(file);
        char[] cuf = new char[ size ];

        //입력소스로부터 최대 len 개의 byte 수를 읽어서, char[] 배열 c의 지정된 위치(off)부터 저장하고 읽은 데이터 수 또는 -1 반환
        bfr.read(cuf, 0, size);
        System.out.println("FileReaderBuffer Read");
        fileMap.put("retData", String.valueOf(cuf) );

    } catch(IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (bfr != null) bfr.close();
        } catch(IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br><br>


# 3. FileOutputStream / FileInputStream 예제

<br>

```java
/**
 * 3-1. FileOutputStream 클래스
 * - 바이트 기반 출력 스트림 최상위 클래스인 OutputStream 를 상속
 * - FileOutputStream 은 OutputStream 을 상속한 클래스로 데이터를 바이트 배열(byte[])로 입력
 * @param fileData
 * @param filePath
 * @param fileName
 */
public static void fileOutputStreamToFile (String fileData, String filePath, String fileName) {
    String d = fileData;
    isMkdirs(filePath);

    File file = new File(filePath + fileName);
    FileOutputStream fos = null;

    try {
        fos = new FileOutputStream(file);

        // FileOutputStream 클래스가 파일에 바이트로 내보내기 때문에 출력할 데이터를 바이트 배열(byte[])로 변환
        byte[] b = d.getBytes();
        fos.write(b);
        fos.flush();

        System.out.println("fileOutputStreamToFile File Created");

    } catch(IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fos != null) fos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

/**
 * 3-2. FileInputStream 클래스
 * - 바이트 기반 출력 스트림 최상위 클래스인 InputStream 를 상속
 * - FileInputStream 은 InputStream 을 상속한 클래스로 데이터를 바이트 배열(byte[])로 출력
 * @param fileMap
 * @param filePath
 * @param fileName
 */
public static void fileInputStream (Map<String, Object> fileMap, String filePath, String fileName) {
    String fileFullPath = filePath + fileName;
    FileInputStream fis = null;

    try {
        fis = new FileInputStream(fileFullPath);

        int size = fis.available();//스트림으로부터 현재 읽을 수 있는 바이트 수를 얻음
        byte[] buf = new byte[ size ];

        //최대 len개의 byte를 읽어서, byte[] 배열 b의 지정된 위치(off)부터 저장하고 읽은 바이트 수 반환
        fis.read(buf, 0, size);
        System.out.println("FileInputStream Read");
        fileMap.put("retData", new String(buf) );

    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (fis != null) fis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br><br>


# 4. BufferedOutputStream / BufferedInputStream

<br>

```java
/**
 * 4-1. BufferedOutputStream 클래스
 * - 바이트 기반 출력 스트림 최상위 클래스인 OutputStream 를 상속
 * - Buffer 을 적용 입력의 효율을 높임
 * @param fileData
 * @param filePath
 * @param fileName
 */
public static void fileBufferedOutputStreamToFile (String fileData, String filePath, String fileName) {

    String d = fileData;
    isMkdirs(filePath);

    FileOutputStream fos = null;
    BufferedOutputStream bos = null;

    try {
        File file = new File(filePath + fileName);
        fos = new FileOutputStream(file);
        bos = new BufferedOutputStream(fos);

        // FileOutputStream 클래스가 파일에 바이트로 내보내기 때문에 출력할 데이터를 바이트 배열(byte[])로 변환
        byte[] b = d.getBytes();
        bos.write(b);
        bos.flush();

        System.out.println("File BufferedOutputStream File Created");

    } catch(IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if(bos != null) bos.close();
        } catch(IOException e) {
            e.printStackTrace();
        }
    }
}

/**
 * 4-2. BufferedInputStream 클래스
 * - 바이트 기반 출력 스트림 최상위 클래스인 InputStream 를 상속
 * - Buffer 을 적용 입력의 출력을 높임
 * @param fileMap
 * @param filePath
 * @param fileName
 */
public static void fileBufferedInputStream (Map<String, Object> fileMap, String filePath, String fileName) {
    String fileFullPath = filePath + fileName;

    FileInputStream fis = null;
    BufferedInputStream bis = null;

    try {
        File file = new File(filePath + fileName);
        fis = new FileInputStream(file);
        bis = new BufferedInputStream(fis);

        int size = bis.available();//스트림으로부터 현재 읽을 수 있는 바이트 수를 얻음
        byte[] b = new byte[ size ];

        //최대 len개의 byte를 읽어서, byte[] 배열 b의 지정된 위치(off)부터 저장하고 읽은 바이트 수 반환
        bis.read(b, 0, size);
        System.out.println("fileBufferedInputStream Read");
        fileMap.put("retData", new String(b) );

    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (bis != null) bis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br><br>


# `5. 파일 데이터 반환 및 파일 복사 예제 설명`

실제로 데이터는 적은데 버퍼를 크게 잡으면 메모리의 낭비가 발생한다.
또는 대용량 파일일 경우 메모리가 부족해져서 java.lang.OutOfMemoryError: Java heap space 발생할 수 있다.
따라서, 사용되는 서비스에 따라 1024, 2048, 4096, 8192 버퍼의 크기를 잡는 것이 일반적이다.

<br>

아래의 2개의 메서드는 `데이터로 반환`과 `파일로 출력`에 출력 방식에 따라 `readLen(읽을 바이트 수)` 값을 임의 설정 시에 
BufferedInputStream 에 있는 파일 데이터 읽어서 출력하는게 다르다.

<br>


```java
/**
 * 5-1. File 을 read 해서 데이터 반환하는 메서드
 *
 * @param fileMap
 * @param filePath
 * @param fileName
 */
public static void readFileToData (Map<String, Object> fileMap, String filePath, String fileName) {
    String fileFullPath = filePath + fileName;
    FileInputStream fis = null;
    BufferedInputStream bis = null;

    try {
        fis = new FileInputStream(fileFullPath);
        bis = new BufferedInputStream(fis);

        // 파일 크기에 따른 값 설정
        int size = bis.available(); //스트림으로부터 현재 읽을 수 있는 바이트 수를 얻음
        System.out.println("파일 전체 바이트 크기=" + size);
        // 파일 내용을 담을 바이트 배열 생성
        byte[] buf = new byte[ size ];

        int bytesRead = 0;
        int offset = 0;
        int readLen = 1024;

        // bis 안에 있는 file 내용을 해당 readLen(byte[]) 값 만큼 반복해서 읽어서 buf(byte[]) 에 담는다
        // ㄴ 전체 물의 양(file 내용)을 바가지(readLen)로 퍼서 욕조에(buf) 담는 개념
        while ((bytesRead = bis.read(buf, offset, readLen)) != -1) {
            System.out.println("bytesRead=" + bytesRead + ", 현재 읽을 수 있는 바이트 수=" + bis.available());
            offset = offset + bytesRead;
            if (offset > buf.length - readLen) {
                readLen = buf.length - offset;
                System.out.println("buf.length="+buf.length+ ", offset=" + offset +",  readLen=" + readLen);
            }
            if (offset >= buf.length) break;
        }
        // file 내용을 모두 읽으며 InputSteam 을 닫고 최종 데이터 결과 buf 을 반환다.
        fileMap.put("retData", new String(buf) );

    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        try {
            if (bis != null) bis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>

> console.log

```
파일 전체 바이트 크기=15615
bytesRead=1024, 현재 읽을 수 있는 바이트 수=14591
bytesRead=1024, 현재 읽을 수 있는 바이트 수=13567
bytesRead=1024, 현재 읽을 수 있는 바이트 수=12543
bytesRead=1024, 현재 읽을 수 있는 바이트 수=11519
bytesRead=1024, 현재 읽을 수 있는 바이트 수=10495
bytesRead=1024, 현재 읽을 수 있는 바이트 수=9471
bytesRead=1024, 현재 읽을 수 있는 바이트 수=8447
bytesRead=1024, 현재 읽을 수 있는 바이트 수=7423
bytesRead=1024, 현재 읽을 수 있는 바이트 수=6399
bytesRead=1024, 현재 읽을 수 있는 바이트 수=5375
bytesRead=1024, 현재 읽을 수 있는 바이트 수=4351
bytesRead=1024, 현재 읽을 수 있는 바이트 수=3327
bytesRead=1024, 현재 읽을 수 있는 바이트 수=2303
bytesRead=1024, 현재 읽을 수 있는 바이트 수=1279
bytesRead=1024, 현재 읽을 수 있는 바이트 수=255
buf.length=15615, offset=15360,  readLen=255
bytesRead=255, 현재 읽을 수 있는 바이트 수=0
buf.length=15615, offset=15615,  readLen=0
1. 개요[편집]
과거 1980년대 전후하여 원격지에 있는 컴퓨터와 근처에 있는 단말기 사이에 있는 수 많은 통신장비 및 네트워크를 다 그리지 않고 구름 모양을 그려서 설명을 하였다. 이후 2000년대에 클라우드 컴퓨팅이 등장하면서 이 개념을 설명하기가 애매하여 이 구름 개념을 따 와서 구름(cloud)이라고 한 것이 클라우드다.

즉, 클라우드의 의미는 컴퓨터 통신망이 복잡한 네트워크 및 서버 구성 등을 알 필요 없이 구름과 같이 내부가 보이지 않고, 일반 사용자는 이 복잡한 내부를 굳이 알 필요도 없이 어디에서나 구름 속의 컴퓨터 자원으로 자기가 원하는 작업을 할 수 있다는 것이다. 이른바 동일한 체험을, 인터넷이 연결된 어디에서나 보장해주는 것이다.

......... 생략 .........
```

<br><br>


```java
/**
 * 5-2. File 을 복사하는 메서드
 * @param targetFilePath
 * @param copyFilePath
*/
public static void copyFile (String targetFilePath, String copyFilePath) {

    FileInputStream fis = null;
    FileOutputStream fos = null;
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;

    try {
        bis = new BufferedInputStream( new FileInputStream(targetFilePath) ); // 파일 입력 스트림 생성 (복사 할 파일)
        bos = new BufferedOutputStream( new FileOutputStream(copyFilePath) ); // 파일 출력 스트림 생성 (복사 될 파일)

        // 파일 크기에 따른 값 설정
        int size = bis.available(); //스트림으로부터 현재 읽을 수 있는 바이트 수를 얻음
        System.out.println("파일 전체 바이트 크기=" + size);
        // 파일 내용을 읽을 바이트 크기
        byte[] buf = new byte[1024];//1024
        int bytesRead = 0;
        int readLen = buf.length;

        // bis 안에 있는 file 내용을 해당 readLen(1024) 값 만큼 읽는 것을 반복
        while ((bytesRead = bis.read(buf, 0, readLen)) != -1) {
            //버퍼 크기만큼 읽을 때마다 출력 스트림에 써준다.
            System.out.println("bytesRead=" + bytesRead + ", 현재 읽을 수 있는 바이트 수=" + bis.available());
            bos.write(buf);
        }

    } catch (Exception e) {
        System.out.println("File I/O Error :  " + e);

    } finally {
        try {
            if (bis != null) bis.close();
            if (bos != null) bos.close();
        } catch (Exception e) {
            System.out.println("Buffer Close Error :  " + e);
        }
    }
}
```

> console.log

```
파일 전체 바이트 크기=15615
bytesRead=1024, 현재 읽을 수 있는 바이트 수=14591
bytesRead=1024, 현재 읽을 수 있는 바이트 수=13567
bytesRead=1024, 현재 읽을 수 있는 바이트 수=12543
bytesRead=1024, 현재 읽을 수 있는 바이트 수=11519
bytesRead=1024, 현재 읽을 수 있는 바이트 수=10495
bytesRead=1024, 현재 읽을 수 있는 바이트 수=9471
bytesRead=1024, 현재 읽을 수 있는 바이트 수=8447
bytesRead=1024, 현재 읽을 수 있는 바이트 수=7423
bytesRead=1024, 현재 읽을 수 있는 바이트 수=6399
bytesRead=1024, 현재 읽을 수 있는 바이트 수=5375
bytesRead=1024, 현재 읽을 수 있는 바이트 수=4351
bytesRead=1024, 현재 읽을 수 있는 바이트 수=3327
bytesRead=1024, 현재 읽을 수 있는 바이트 수=2303
bytesRead=1024, 현재 읽을 수 있는 바이트 수=1279
bytesRead=1024, 현재 읽을 수 있는 바이트 수=255
bytesRead=255, 현재 읽을 수 있는 바이트 수=0
```

<br><br>


## 기타
---

```java
/**
* 디렉터리 생성
* @param filePath
*/
public static void isMkdirs(String filePath) {
    File fileDir = new File(filePath);
    if (! fileDir.exists()) {
        fileDir.mkdirs();
    }
}

/**
* file size(크기) 리턴
* @param f
* @return
*/
public static int isFileSize(File f) {
    int retSize = 0;
    try {
        long fileSize = f.length();

        if (fileSize >= 2147483649L) {
            throw new Exception("long to int casting Range Over Error");
        }
        retSize = Long.valueOf(fileSize).intValue();

    } catch (Exception e) {
        e.printStackTrace();
    }
    return retSize;
}
```

<br>

---

