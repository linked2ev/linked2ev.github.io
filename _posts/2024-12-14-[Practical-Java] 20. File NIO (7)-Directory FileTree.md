---
layout: post
title:  "[Practical-Java] 20. File NIO (7)-Directory FileTree"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

파일 NIO 에서는 디렉터리의 트리 구조를 지원하기 위해 `FileVisitor 인터페이스`를 제공한다. 해당 인터페이스를 직접 구현해서 사용해도 되고 자바에서 기본 제공하는 SimpleFileVisitor 클래스를 이용해서 처리해도 된다.

<br>


# 1. `FileVisitor` 인터페이스

FileVisitor 인터페이스의 메서드에 대한 설명이다. `BasicFileAttributes` 클래스는 파일이나 다른 파일 시스템 객체의 기본 속성값을 읽는 데 사용된다.

| 메서드 이름                 | 호출 시점                                 | 설명 |
|-------------------------|----------------------------------------|-----|
| `preVisitDirectory(T dir, BasicFileAttributes attrs)` | 디렉터리를 방문하기 전                          | 디렉터리에 진입하기 전에 수행할 작업이 정의된다. 예를 들어, 접근 권한을 확인하거나 로깅을 수행할 수 있다. |
| `visitFile(T file, BasicFileAttributes attrs)`       | 파일을 방문할 때                               | 파일을 방문했을 때 수행할 작업이 정의된다. 파일을 읽고 처리하거나 정보를 수집하는 등의 작업이 포함된다. |
| `visitFileFailed(T file, IOException exc)`           | 파일 방문이 실패했을 때                          | 파일을 열거나 읽는 데 실패했을 때 호출된다. 이 메서드를 통해 오류를 처리하거나 로깅할 수 있다. |
| `postVisitDirectory(T dir, IOException exc)`         | 디렉터리의 모든 항목을 방문한 후                    | 디렉터리 방문이 끝난 후에 호출된다. 디렉터리의 처리를 마무리하고, 예외 처리를 수행할 수 있다. |

<br>

### 예제 코드

```java
import java.io.IOException;
import java.nio.file.*;
import java.nio.file.attribute.BasicFileAttributes;

/**
 * 파일 시스템의 탐색을 처리하면서 파일과 디렉터리를 재귀적으로 방문
 */
public class FileVisitorExample {

    public static void main(String[] args) {

        Path dirPath = Paths.get("/exmaple/java");  // 탐색을 시작할 경로
        long[] totalSize = {0}; //final 개념

        try {
            Files.walkFileTree(dirPath, new FileVisitor<Path>() {
                //디렉터리 진입하기 전에 호출
                @Override
                public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
                    System.out.println("preVisitDirectory: " + dir);
                    return FileVisitResult.CONTINUE;
                }

                //파일에 접근하면서 호출
                @Override
                public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
                    System.out.println("visitFile: " + file);
                    totalSize[0] += attrs.size();
                    return FileVisitResult.CONTINUE;
                }

                @Override
                public FileVisitResult visitFileFailed(Path file, IOException exc) {
                    System.err.println("visitFileFailed: " + file);
                    return FileVisitResult.CONTINUE;
                }

                //디렉터리 진입한 후에 호출
                @Override
                public FileVisitResult postVisitDirectory(Path dir, IOException exc) {
                    if (exc == null) {
                        System.out.println("postVisitDirectory: " + dir);
                        return FileVisitResult.CONTINUE;
                    } else {
                        System.err.println("Error postVisitDirectory: " + dir);
                        return FileVisitResult.CONTINUE;
                    }
                }
            });

            System.out.println("Total size of all files: " + totalSize[0] + " bytes");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br><br>


# 2. walk 메서드와 walkFileTree 메서드

Java의 `Files` 클래스는 파일 시스템을 다루는 유용한 메서드들을 제공하는데, 그 중 `walk` 메서드와 `walkFileTree` 메서드는 디렉터리 및 파일 트리를 탐색하는 데 사용된다. 이 두 메서드는 유사한 기능을 제공하지만 사용 방식과 목적에 있어 다소 차이가 있다. `walk` 메서드는 간단한 탐색과 필터링, 데이터 집계 등 스트림을 통한 처리에 적합하고, `walkFileTree`는 파일 시스템의 구조적 변경, 복잡한 조건에 따른 탐색 및 처리 등에 더 적합한다.


## 2-1. walk 메서드

`walk` 메서드는 Java 8부터 도입된 스트림 API를 사용하여 디렉터리를 탐색한다. 이 메서드는 **`Stream<Path>`를 반환**하며, 이 스트림을 통해 디렉터리 및 그 하위 파일을 게으른 방식으로 탐색할 수 있다. 이는 디렉터리의 내용을 실시간으로 처리하면서 필요에 따라 멈추거나 필터링을 적용할 수 있음을 의미한다. 

- **walk(Path start, FileVisitOption... options)**
- **walk(Path start, int maxDepth, FileVisitOption... options)**

#### 파라미터 설명

- **Path start**: 탐색을 시작할 디렉터리의 경로
- **int maxDepth**: 탐색할 최대 깊이. 지정하지 않으면 기본값은 디렉터리 트리의 최대 깊이까지 탐색
- **FileVisitOption... options**: 탐색 옵션, 예를 들어 `FOLLOW_LINKS`는 심볼릭 링크를 따라가야 할지 여부를 지정

#### 반환 타입

- **`Stream<Path>`**를 반환

<br>

### Files.walk 예제 코드

```java
public class WalkExample {
    public static void main(String[] args) {
        Path startingDir = Paths.get("/example/java");
        String pattern = "glob:*.{java,txt}";  // glob 패턴을 사용하여 .java 또는 .txt 파일 매칭

        // 파일 시스템의 기본 PathMatcher 를 사용하여 패턴을 생성
        final PathMatcher matcher = FileSystems.getDefault().getPathMatcher(pattern);

        try (Stream<Path> stream = Files.walk(startingDir)) {
            stream.filter(p -> matcher.matches(p.getFileName()))  // 파일 이름이 패턴과 일치하는지 확인
                    .forEach(System.out::println);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>

## 2-2. walkFileTree 메서드

`walkFileTree` 메서드는 좀 더 전통적인 방식의 디렉터리 탐색을 제공하며, `FileVisitor` 인터페이스의 구현체를 통해 디렉터리와 파일을 방문한다. 이 메서드는 방문하는 각 경로에 대해 `FileVisitor` 메서드를 호출하며, 방문 순서를 제어하고, 탐색을 중지하거나 계속할 수 있다.

- **walkFileTree(Path start, FileVisitor<? super Path> visitor)**
- **walkFileTree(Path start, Set<FileVisitOption> options, int maxDepth, FileVisitor<? super Path> visitor)**

#### 파라미터 설명

- **Path start**: 파일 트리 탐색을 시작할 디렉터리의 경로
- **Set<FileVisitOption> options**: 파일 탐색 옵션
- **int maxDepth**: 탐색할 최대 깊이
- **FileVisitor<? super Path> visitor**: 파일 또는 디렉터리를 방문할 때 호출할 `FileVisitor` 인터페이스의 구현체

#### 반환 타입

- **void**

<br>

### Files.walkFileTree 예제 코드

```java
class FilesFinder implements FileVisitor<Path> {//extends SimpleFileVisitor<Path> 
    private final PathMatcher matcher;  //패턴 정의 객체
    private int numMatches = 0;

    // FileFinder 생성자에서 매칭할 파일 패턴을 정의
    public FilesFinder(String pattern) {
        matcher = FileSystems.getDefault().getPathMatcher("glob:" + pattern);
    }

    private void isMatched(Path file) {
        if (matcher.matches(file.getFileName())) {
            numMatches++;
            System.out.println(file);
        }
    }

    @Override
    public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
        System.out.println("visit file: " + file);
        isMatched(file);
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult visitFileFailed(Path file, IOException exc) {
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult postVisitDirectory(Path dir, IOException exc) {
        return FileVisitResult.CONTINUE;
    }

    public int getNumMatches() {
        return numMatches;
    }
}

/**
 *  Files.walkFileTree 예제
 */
public class WalkFileTreeExample {
    public static void main(String[] args) {
        Path startingDir = Paths.get("/example/java");
        String pattern = "*.{java,txt}";

        FilesFinder fileFinder = new FilesFinder(pattern);

        try {
            Files.walkFileTree(startingDir, fileFinder);
            System.out.println("matches file count: " + fileFinder.getNumMatches());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>


# 3. Find 메서드

`Files.find`는 Files.walk와 유사하게 디렉터리를 재귀적으로 탐색하지만, 사용자가 제공하는 `BiPredicate<Path, BasicFileAttributes>`를 사용하여 파일과 디렉터리를 필터링 할 수 있다. BasicFileAttributes 클래스를 활용해 파일 속성정보를 가져와 커스텀 필터가 가능한게 장점이다.

### Files.find 예제 코드

```java
public class FindExample {
    public static void main(String[] args) {
        Path startingDir = Paths.get("/example/java");
        String pattern = "glob:*.{java,txt}";
        
        final PathMatcher matcher = FileSystems.getDefault().getPathMatcher(pattern);

        // 파일과 해당 파일의 속성을 검사하는 BiPredicate 함수형 인터페이스 객체 생성
        BiPredicate<Path, BasicFileAttributes> predicate = (path, attrs) ->
                matcher.matches(path.getFileName()) && attrs.isRegularFile();

        try (Stream<Path> stream = Files.find(startingDir, Integer.MAX_VALUE, predicate)) {
            stream.forEach(System.out::println);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br><br>


---