---
layout: post
title:  "[Practical-Java] 14. File NIO (1)"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Java NIO (New Input/Output)는 Java 1.4 버전에서 소개된 입력 및 출력의 새로운 방식으로, 기존의 표준 Java I/O를 보완하여 더 높은 성능과 기능을 제공합니다. `Java NIO는 논블로킹(non-blocking) 입출력, 버퍼 관리, 채널(channel)을 이용한 데이터 전송 등의 특징`을 가지고 있다.

<br>


# File NIO

기존의 Java IO 시스템은 스트림 기반의 블로킹 I/O를 사용했는데, 이는 데이터를 읽거나 쓸 때 해당 작업이 완료될 때까지 호출 스레드가 대기 상태에 머무르는 반면, Java NIO는 논블로킹 방식을 지원하여, 데이터를 처리하는 동안에도 다른 작업을 동시에 수행할 수 있게 한다. 그래서 입출력 처리 과정에서 발생할 수 있는 대기 시간을 줄일 수 있다.

<br>


# 1. NIO 주요 구성 요소

1. **채널(Channel)**:
   - `채널은 기본적으로 데이터의 소스나 목적지에 대한 연결을 제공`한다. 파일, 소켓 등 다양한 데이터 소스와의 입출력 작업을 위한 역할을 한다. 채널은 읽기, 쓰기, 연결, 수락 등 다양한 작업을 지원하며, 채널 자체는 데이터를 직접 저장하지 않는다.

2. **버퍼(Buffer)**:
   - `버퍼는 데이터를 저장하는 메모리 구조`로, Java NIO에서 데이터 처리의 핵심 요소이다. 버퍼는 데이터를 일시적으로 저장하며, 채널을 통해 데이터를 읽거나 쓰기 전에 중간 저장소로 사용된다.

3. **`셀렉터(Selector)`**: 
   - `셀렉터는 단일 스레드를 사용하여 여러 채널의 입출력 상태를 모니터링할 수 있게 해주는 구성 요소`입니다. 즉, 하나의 스레드가 여러 채널의 입출력을 동시에 관리(처리)할 수 있게 해주는 구성 요소로 이를 통해 여러 네트워크 연결을 동시에 관리할 수 있으며, 어떤 채널이 데이터를 준비하고 있는지, 어떤 채널이 더 이상 데이터를 받지 않는지 등을 파악할 수 있다. 그래서 `셀렉터는 복수 개의 채널 중에서 준비완료된 채널을 선택하는 방법을 제공한다.`
  
<br>


# 2. NIO 특징

1. **논블로킹 & 비동기 IO**: NIO는 채널을 통해 비동기적으로 입출력을 수행할 수 있으며, 논블로킹 방식을 제공하여 IO 작업 중에도 다른 작업을 계속 수행할 수 있습니다.
2. **버퍼 기반 데이터 처리**: 모든 데이터는 버퍼를 통해 처리되므로, 데이터를 한번에 묶어서 빠르게 전송할 수 있는 효율적인 데이터 관리가 가능합니다.
3. **멀티플렉싱 IO**: 셀렉터를 사용하여 하나의 스레드가 여러 IO 채널을 관리할 수 있습니다. 이는 자원의 효율적 사용과 시스템의 성능 향상을 돕습니다.

Java NIO는 서버가 다수의 클라이언트와 동시에 통신해야 할 때 매우 유용합니다. 예를 들어, 네트워크 서버를 구현할 때 하나의 스레드로 여러 클라이언트의 연결을 관리하고 데이터를 처리할 수 있다. 

<br>


# 3. Java NIO 관련 주요 패키지

| 패키지명                  | 설명                                                                                       |
|-------------------------|------------------------------------------------------------------------------------------|
| **java.nio**            | NIO의 기본, 핵심 클래스와 인터페이스를 정의해 놓은 패키지이다. 네트워크 파일 등의 자원으로부터 데이터를 처리하기 위한 기본 기능을 제공한다. |
| **java.nio.channels**   | 파일 채널, TCP 채널, UDP 채널 등의 파일이나 소켓과 같이 I/O 작업을 수행하기 위한 연결 정보가 있는 채널을 정의한다. 특별히 논블로킹 I/O와 멀티 처리 등을 사용하기 위한 기능을 제공한다. |
| **java.nio.channels.spi** | java.nio.channels 패키지를 위한 서비스 제공자 클래스이다. |
| **java.nio.charset**    | 바이트와 유니코드 캐릭터들을 상호 변환하기 위한 캐릭터셋과 인코더, 디코더를 정의한다. |
| **java.nio.charset.spi** | java.nio.charset 패키지를 위한 서비스 제공자 클래스이다. |
| **java.nio.file**       | JVM이 파일, 파일 속성, 파일 시스템에 접근하기 위한 클래스와 인터페이스를 제공한다. |
| **java.nio.file.attribute** | 파일 속성에 접근하기 위한 클래스와 인터페이스를 제공합니다. |
| **java.nio.file.spi**   | java.nio.file 패키지를 위한 서비스 제공자 클래스이다. |

<br>


# 4. Java IO와 NIO의 차이점

기존 I/O는 `Stream 기반`으로 작동합니다. 이는 데이터를 순차적(`Sync&Blocking`)으로 읽고 쓰는 방식이고   
NIO는 `Non-blocking` I/O 모델을 기반으로 `Channel과 Buffer를 사용`하여 데이터를 `Aync` 처리한다.


| 구분               | IO                  | NIO                      |
|-------------------|---------------------|--------------------------|
| **입출력 방식**     | `스트림 방식`            | `채널 방식`                  |
| **버퍼 방식**      | 논버퍼               | 버퍼                       |
| **비동기 방식**    | 지원 안함              | 지원                      |
| **블로킹/논블로킹 방식** | 블로킹방식만 지원       | 블로킹/논블로킹 둘다 지원    |

<br>

### 1. 입출력 방식

- **IO (Stream, 스트림 방식)**: 
  - 데이터는 스트림을 통해 순차적으로 처리된다. 스트림은 데이터가 일방통행으로 흐르는 통로를 의미하며, 입력 스트림과 출력 스트림이 각각 필요하다.
- **NIO (Channel, 체널 방식)**:
  - 채널은 스트림과 다르게 양방향 입출력이 가능하므로 `채널을 통해 데이터가 양방향`으로 흐를 수 있다. 채널은 입출력을 동시에 처리할 수 있는 구조로, 데이터를 더 유연하게 다룰 수 있다.

### 2. 버퍼 방식

- **IO (Non-Buffer, 논버퍼)**: 
  - 스트림은 버퍼링 없이 바로 데이터를 소스나 목적지로 전달한다. 이 방식은 각 바이트를 처리할 때마다 입출력을 수행하므로 효율성이 낮다.
- **NIO (Buffer, 버퍼)**:
  - `모든 데이터 작업은 버퍼를 통해 이루어진다.` 버퍼는 데이터 블록을 임시 저장하여, 버퍼에서 데이터를 읽거나 쓰면서 네트워크나 파일 시스템과의 상호 작용한다. 데이터 처리를 한 덩어리로 묶어서 더 효율적으로 처리할 수 있게 한다.

### 3. 비동기 방식

- **IO (Sync, 지원 안함)**: 
  - IO스레드가 블로킹되면 다른 일을 할 수 없고 블로킹을 빠져나오기 위해 인터럽트할 수도 없기에 대기상태이다. 유일한 방법은 스트림을 닫는 것으로 자원의 비효율적일 수 있다.
- **NIO (Async, 지원)**:
  - `NIO는 비동기 입출력을 지원하여, 작업을 요청한 후 다른 작업으로 넘어갈 수 있다.` 준비된 데이터가 있을 때만 처리를 계속하므로 리소스를 보다 효율적으로 사용할 수 있다.

### 4. 블로킹/논블로킹 방식

- **IO (블로킹방식만 지원)**: 데이터를 읽고 쓰는 동안 스레드가 해당 작업에 묶여 있어야 하며, 다른 작업을 수행할 수 없다. 이로 인해 동시 처리 능력에 한계가 있다.
- **NIO (블로킹/논블로킹 둘다 지원)**: Java NIO의 논블록킹을 사용하면 스레드가 채널에서 데이터 읽기를 요청할 수 있으며 현재 사용 가능한 데이터만 가져오거나 현재 사용 가능한 데이터가 없는 경우 아무 것도 가져오지 않는다. 데이터를 읽을 수 있을 때까지 블로킹 상태로 유지하는 대신 스레드는 다른 작업을 계속할 수 있다. `NIO 논블로킹의 핵심 객체는 멀티플렉서인 셀렉터(Selector)이다. 셀렉터는 복수 개의 채널 중에서 준비완료된 채널을 선택하는 방법을 제공`한다. 

<br>


# 5. NIO 주요 클래스와 인터페이스

Java NIO의 `java.nio.file` 패키지는 파일 시스템을 처리하는 데 필요한 클래스와 인터페이스를 제공한다. 

<br>

1. **Path**
   - 파일이나 디렉토리의 위치를 추상화한 인터페이스다. 파일 시스템 내의 경로를 표현하며, 파일을 생성, 삭제, 또는 접근하는 작업에 사용한다.

2. **Paths**
   - `Path` 인스턴스를 생성하기 위한 유틸리티 클래스다. 주로 `Paths.get(String uri)` 메서드를 통해 문자열로부터 `Path` 객체를 얻는다.

3. **Files**
   - 파일 및 디렉토리를 생성, 삭제 및 수정하는 데 사용되는 정적 메서드들을 제공하는 유틸리티 클래스다. 파일의 메타데이터를 읽거나, 파일을 복사, 이동하는 등의 작업을 수행한다.

4. **FileSystem**
   - 파일 시스템의 인스턴스를 표현하는 클래스로, 파일 시스템과의 상호작용을 가능하게 한다. 예를 들어, 파일 시스템의 루트 디렉토리나 파일 저장소를 얻는 데 사용한다.

5. **FileSystems**
   - 기본 파일 시스템 또는 사용자 정의 파일 시스템을 얻기 위한 유틸리티 클래스다.

6. **FileStore**
   - 파일 저장소(하드웨어 디스크 파티션 등)의 뷰를 제공하며, 사용 가능한 공간이나 파일 저장소의 유형 등의 정보를 제공한다.

7. **WatchService**
   - 디렉토리 (또는 파일)에 대한 변경사항을 감시하는 메커니즘을 제공한다. 예를 들어 파일 생성, 삭제 또는 수정 시 이벤트를 받을 수 있다.

8. **FileVisitor** 및 **SimpleFileVisitor**
   - 파일 트리를 순회할 때 사용하는 인터페이스와 이를 간단히 구현한 추상 클래스다. 파일 트리의 각 파일을 방문할 때 수행할 작업을 정의한다.

9. **DirectoryStream**
   - 디렉토리 내의 파일을 반복 처리하기 위한 인터페이스다. `Files.newDirectoryStream(Path dir)`을 사용하여 디렉토리 스트림을 생성할 수 있다.

<br>

# 6. 파일 속성 정보 확인

## 기본 파일 속성 **BasicFileAttributes** 인터페이스

```java
Path file = Paths.get("example.txt");
BasicFileAttributes attrs = Files.readAttributes(file, BasicFileAttributes.class);
```

| 메서드명             | 설명                                                         |
|-------------------|------------------------------------------------------------|
| `creationTime()`  | 파일이 생성된 시간을 반환합니다. 파일의 생성 시간을 나타내는 `FileTime` 객체를 리턴합니다. |
| `lastModifiedTime()` | 파일이 마지막으로 수정된 시간을 반환합니다. 파일의 마지막 수정 시간을 나타내는 `FileTime` 객체를 리턴합니다. |
| `lastAccessTime()` | 파일이 마지막으로 접근된 시간을 반환합니다. 파일의 마지막 접근 시간을 나타내는 `FileTime` 객체를 리턴합니다. |
| `size()`           | 파일의 크기를 바이트 단위로 반환합니다.                                       |
| `isDirectory()`    | 파일이 디렉토리인지 여부를 반환합니다. `true`는 디렉토리, `false`는 디렉토리가 아님을 나타냅니다. |
| `isRegularFile()`  | 파일이 일반 파일인지 여부를 반환합니다. `true`는 일반 파일, `false`는 일반 파일이 아님을 나타냅니다. |
| `isSymbolicLink()` | 파일이 심볼릭 링크인지 여부를 반환합니다. `true`는 심볼릭 링크, `false`는 심볼릭 링크가 아님을 나타냅니다. |
| `isOther()`        | 파일이 디렉토리, 일반 파일, 심볼릭 링크 중 어느 것도 아닐 경우 `true`를 반환합니다. |
| `fileKey()`        | 파일의 시스템 고유 키를 반환합니다. 이 키는 파일 시스템 내에서 파일을 식별하는 데 사용될 수 있는 객체입니다. |

<br>


## POSIX 에서 파일 속성 **PosixFileAttributes** 인터페이스

```java
PosixFileAttributes posixAttrs = Files.readAttributes(file, PosixFileAttributes.class);
```

| 메서드명                    | 설명                                               |
|-------------------------|--------------------------------------------------|
| `owner()`               | 파일의 소유자를 나타내는 `UserPrincipal` 객체를 반환합니다.      |
| `group()`               | 파일이 속한 그룹을 나타내는 `GroupPrincipal` 객체를 반환합니다.  |
| `permissions()`         | 파일의 권한을 나타내는 `Set<PosixFilePermission>` 객체를 반환합니다. |


## MS Windows 에서 파일 속성 **DosFileAttributes** 인터페이스

```java
DosFileAttributes dosAttrs = Files.readAttributes(file, DosFileAttributes.class);
```

| 메서드명                    | 설명                                               |
|-------------------------|--------------------------------------------------|
| `isReadOnly()`         | 파일이 읽기 전용인지 여부를 반환합니다. `true`는 읽기 전용입니다. |
| `isHidden()`           | 파일이 숨겨진 파일인지 여부를 반환합니다. `true`는 숨겨진 파일입니다. |
| `isArchive()`          | 파일이 아카이브 파일인지 여부를 반환합니다. `true`는 아카이브 파일입니다. |
| `isSystem()`           | 파일이 시스템 파일인지 여부를 반환합니다. `true`는 시스템 파일입니다. |


<br>


# 5. I/O 와 NIO 비교

Java에서 I/O 와 NIO 비교하기 위해 간단한 예제코드를 보자. I/O는 File 클래스를 사용하고, NIO는 Paths와 Files 클래스 사용한다.

<br>

## 5-1. IO 예제 : File 클래스

```java
// 파일 생성
File file = new File("example.txt");
try {
   if (file.createNewFile()) {
         System.out.println("파일 생성: " + file.getName());
   } else {
         System.out.println("파일이 이미 존재합니다.");
   }
} catch (IOException e) {
   System.out.println("오류가 발생했습니다.");
   e.printStackTrace();
}

// 디렉터리 생성
File dir = new File("myDirectory");
if (dir.mkdir()) {
   System.out.println("디렉터리 생성: " + dir.getName());
} else {
   System.out.println("디렉터리 생성 실패 또는 이미 존재합니다.");
}

// 여러 단계 디렉터리 생성
File dirs = new File("myDir/SubDir1/SubDir2");
if (dirs.mkdirs()) {
   System.out.println("여러 디렉터리가 성공적으로 생성되었습니다.");
} else {
   System.out.println("디렉터리 생성에 실패했습니다.");
}

// 파일 존재 확인
if (file.exists()) {
   System.out.println("파일 존재: " + file.getName());
} else {
   System.out.println("파일이 존재하지 않습니다.");
}

// 파일 삭제
if (file.delete()) {
   System.out.println("파일 삭제됨: " + file.getName());
} else {
   System.out.println("파일 삭제 실패.");
}

// 파일 복제 (복사)
File sourceFile = new File("example.txt");
File destFile = new File("copy_example.txt");
try {
   if (sourceFile.createNewFile()) { // 복사할 파일을 임시로 생성
         System.out.println("원본 파일 생성됨: " + sourceFile.getName());
         java.nio.file.Files.copy(sourceFile.toPath(), destFile.toPath());
         System.out.println("파일 복사됨: " + destFile.getName());
   } else {
         System.out.println("원본 파일이 이미 존재하여 복사하지 않음.");
   }
} catch (IOException e) {
   System.out.println("파일 복사 중 오류 발생.");
   e.printStackTrace();
}

// 파일 이동
File moveFile = new File("move_example.txt");
try {
   if (destFile.renameTo(moveFile)) {
         System.out.println("파일 이동됨: " + moveFile.getName());
   } else {
         System.out.println("파일 이동 실패.");
   }
} catch (Exception e) {
   System.out.println("파일 이동 중 오류 발생.");
   e.printStackTrace();
}
```

<br>


## 5-2. NIO 예제 : Paths와 Files 클래스

```java
// 파일 생성
Path pathToFile = Paths.get("example.txt");
try {
   Files.createFile(pathToFile);
   System.out.println("파일 생성: " + pathToFile);
} catch (IOException e) {
   System.out.println("오류 발생 또는 파일이 이미 존재합니다.");
   e.printStackTrace();
}

// 디렉터리 생성
Path pathToDir = Paths.get("myNioDirectory");
try {
   Files.createDirectory(pathToDir);
   System.out.println("디렉터리 생성: " + pathToDir);
} catch (IOException e) {
   System.out.println("오류 발생 또는 디렉터리가 이미 존재합니다.");
   e.printStackTrace();
}

// 여러 단계 디렉터리 생성
Path pathToDirs = Paths.get("myNioDir/SubDir1/SubDir2");
try {
   Files.createDirectories(pathToDirs);
   System.out.println("디렉터리들이 생성되었습니다: " + pathToDirs);
} catch (IOException e) {
   System.out.println("디렉터리들을 생성하는 데 실패했습니다.");
   e.printStackTrace();
}

// 파일 존재 확인
if (Files.exists(pathToFile)) {
   System.out.println("파일 존재: " + pathToFile);
} else {
   System.out.println("파일이 존재하지 않습니다.");
}

// 파일 삭제
try {
   Files.deleteIfExists(pathToFile);
   System.out.println("파일이 삭제되었습니다: " + pathToFile);
} catch (IOException e) {
   System.out.println("파일 삭제에 실패했습니다.");
   e.printStackTrace();
}

// 파일 복제 (복사)
Path sourceFile = Paths.get("example.txt");
Path targetFile = Paths.get("copy_example.txt");
try {
   Files.copy(sourceFile, targetFile, StandardCopyOption.REPLACE_EXISTING);
   System.out.println("파일 복사됨: " + targetFile);
} catch (IOException e) {
   System.out.println("파일 복사에 실패했습니다.");
   e.printStackTrace();
}

// 파일 이동
Path moveTargetFile = Paths.get("moved_example.txt");
try {
   Files.move(targetFile, moveTargetFile, StandardCopyOption.REPLACE_EXISTING);
   System.out.println("파일 이동됨: " + moveTargetFile);
} catch (IOException e) {
   System.out.println("파일 이동에 실패했습니다.");
   e.printStackTrace();
}
```

<br><br>


---
