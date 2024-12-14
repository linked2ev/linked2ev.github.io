---
layout: post
title:  "[Practical-Java] 19. File NIO (6)-Directory"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Java NIO의 `Files` 클래스에서 제공하는 기본적인 디렉터리 관련 메서드나 기능에 대한 포스팅이다.

<br><br>


# 1. 디렉터리를 생성

1. **createDirectory** 메서드
   - **createDirectory(Path dir, FileAttribute<?>... attrs)**
   - `createDirectory` 메서드는 지정된 단일 디렉터리를 생성한다. 이 메서드는 해당 디렉터리의 바로 상위 디렉터리가 이미 존재해야 하며, 상위 디렉터리가 없을 경우 `NoSuchFileException`을 발생시킨다.
    ```java
    Path singleDir = Paths.get("/tmp/newDir");
    Files.createDirectory(singleDir, PosixFilePermissions.asFileAttribute(PosixFilePermissions.fromString("rwxr-x---")));
    ```

1. **createDirectories** 메서드
   - **createDirectories(Path dir, FileAttribute<?>... attrs)**
   - `createDirectories` 이 메서드는 주어진 경로에 필요한 모든 상위 디렉터리를 포함하여 디렉터리를 생성합니다. 이미 디렉터리가 있는 경우에는 아무런 작업도 수행하지 않는다.
    ```java
    Path multipleDirs = Paths.get("/tmp/path/to/newDirs");
    Files.createDirectories(multipleDirs, PosixFilePermissions.asFileAttribute(PosixFilePermissions.fromString("rwxr-x---")));
    ```

<br><br>


# 2. 디렉터리 목록 조회

1. **Files.newDirectoryStream(Path dir)** 메서드
   - 자바 NIO의 `Files` 클래스를 사용하여 디렉터리 내의 파일과 하위 디렉터리 목록을 조회하는 작업은 효율적으로 수행할 수 있다. 이 기능은 `Files.newDirectoryStream(Path dir)` 메서드를 사용하여 구현한다. 이 메서드는 디렉터리 경로를 인자로 받아 해당 디렉터리 내의 모든 파일과 디렉터리를 나타내는 `DirectoryStream`을 반환한다.
    ```java
    Path dir = Paths.get("/path/directory");

    try (DirectoryStream<Path> stream = Files.newDirectoryStream(dir, "*.xml")) {
        for (Path file : stream) {
            System.out.println(file.getFileName()); // 파일 또는 디렉터리 이름 출력
        }
    } catch (IOException | DirectoryIteratorException e) {
        e.printStackTrace();
    }
    ```

2. **Files.newDirectoryStream(Path dir, String glob)** 메서드
    - glob 파라미터로 필터링이 가능하다.
    ```java
    try (DirectoryStream<Path> stream = Files.newDirectoryStream(dir, "*.xml")) {
    ```

3. **Files.newDirectoryStream(Path dir, DirectoryStream.Filter<? super Path> filter)** 메서드
    - Filter 객체 필터링이 가능하다.
    ```java
    Path dir = Paths.get("C:/example"); // 대상 디렉터리 경로

    // DirectoryStream.Filter 구현을 통한 필터 생성
    Filter<Path> filter = new Filter<Path>() {
        @Override
        public boolean accept(Path entry) throws IOException {
            return entry.toString().endsWith(".txt");
        }
    };
    try (DirectoryStream<Path> stream = Files.newDirectoryStream(dir, filter)) {
    ```

<br><br>


# 3. 디렉터리 목록 필터링

1. **`FilenameFilter` 인터페이스의 accept 메서드**
   - **accept(File dir, String name)**
   - 디렉터리 내의 파일이나 다른 디렉터리 이름을 기반으로 필터링한다.
    ```java
    File directory = new File("C:/example"); // 대상 디렉터리 경로

    // FilenameFilter를 구현하는 익명 클래스
    // 디렉터리에서 필터를 사용하여 파일 리스트를 가져옴
    String[] files = directory.list(new FilenameFilter() {
        @Override
        public boolean accept(File dir, String name) {
            return name.toLowerCase().endsWith(".xml");
        }
    );

    for (String file : files) {
        System.out.println(file);
    }
    ```

<br><br>


# 4. root 디렉터리 조회

1. **FileSystems.getDefault().getRootDirectories()** 메서드
    - Java NIO에서 루트 디렉터리를 조회하는 작업은 `FileSystems` 클래스를 사용하여 `시스템의 파일 시스템에 대한 접근`을 제공합니다. 이를 통해 여러 플랫폼에서 사용 가능한 모든 루트 디렉터리를 가져올 수 있다.
    - 이 메서드는 시스템의 모든 루트 디렉터리를 나타내는 `Iterable<Path>` 객체를 반환한다.
    ```java
    FileSystem fs = FileSystems.getDefault();
    Iterable<Path> rootDirectories = fs.getRootDirectories();

    for (Path path : rootDirectories) {
        System.out.println(path);//Unix 기반 : 일반적으로 `/`, Windows : `C:\`, `D:\` 등 드라이브 루트
    }
    ```

<br><br>


---