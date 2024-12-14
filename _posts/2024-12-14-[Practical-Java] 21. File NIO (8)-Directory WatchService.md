---
layout: post
title:  "[Practical-Java] 21. File NIO (8)-Directory WatchService"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

전통적으로 파일이나 디렉터리 변경은 반복문을 계속 수행함으로서 변경 여부를 확인하는 방법으로 감지했다. 이러한 방법은 for, while문으로 반복문을 돌거나 별도의 스레드나 백그라운드를 구현을 해야하기에 개발자에게 부담이 될 수 있다.

그래서 파일 NIO에서는 변경 감지하기 위해 파일 Watch API를 제공함으로서 모니터링하고자 하는 자원의 스레드를 생성하고 스레드 풀에 등록해서 관리하면 변화가 생겼을 때 이벤트를 발생시켜 알려준다.

<br>


# 1. 디렉터리 변경 감지

Java NIO의 `WatchService` API는 파일 시스템의 특정 디렉터리(또는 디렉터리 트리)를 모니터링하고, 파일 생성, 삭제, 수정과 같은 이벤트가 발생할 때 이를 감지할 수 있게 해준다. 이 API를 사용하면 특정 디렉터리에 대한 변경 사항을 실시간으로 감지하고 반응하는 애플리케이션을 구현할 수 있다.

<br>

### ◼︎ WatchService 예제 코드

- **WatchService**: `WatchService 는 운영체제 전반에 걸쳐서 제공되는 서비스이기에 FileSystems 객체를 사용`, 이벤트 알림을 위한 서비스를 생성하고 관리. `FileSystems.getDefault().newWatchService()`를 호출하여 인스턴스를 생성
- **Watchable**: `Path` 클래스는 `Watchable` 인터페이스를 구현하고 있어, `WatchService`에 등록될 수 있다.
- **WatchKey**: 등록된 디렉터리에서 발생하는 이벤트를 나타냅니다. 각 키는 한 디렉터리를 모니터링하며, 이벤트가 발생하면 이를 통해 접근할 수 있다.
- **WatchEvent**: 이벤트 자체를 나타내며, 이벤트의 종류(생성, 삭제, 수정), 이벤트가 발생한 파일 또는 디렉터리에 대한 정보를 포함

```java
package com.modern.java.practical.filenio.watchservice;

import java.nio.file.*;
import java.nio.file.attribute.*;
import java.io.IOException;
import static java.nio.file.StandardWatchEventKinds.*;

/**
 * WatchService API 는 파일 시스템의 특정 디렉터리(또는 디렉터리 트리)를 모니터링하고, 파일 생성, 삭제, 수정과 같은 이벤트가 발생할 때 이를 감지
 */
public class DirectoryWatchServiceExample {
    public static void main(String[] args) {

        Path targetDir = Paths.get("/path/to/watch"); // 감시할 디렉터리 경로 설정

        //모니터링 하는 WatchService 객체 생성
        try (WatchService watchService = FileSystems.getDefault().newWatchService()) {
            /*
            # StandardWatchEventKinds
            - ENTRY_CREATE: 파일이 생성될 때 발생
            - ENTRY_DELETE: 파일이 삭제될 때 발생
            - ENTRY_MODIFY: 파일이 수정될 때 발생
            - OVERFLOW: 이벤트가 너무 많아 일부 이벤트가 손실될 때 발생
             */
            //모니터링 대상 경로를 WatchService 에 등록
            targetDir.register(watchService, ENTRY_CREATE, ENTRY_DELETE, ENTRY_MODIFY);
            System.out.println("Monitoring directory for changes...");

            while (true) {
                WatchKey key;
                try {
                    key = watchService.take(); // 이벤트를 기다림
                } catch (InterruptedException x) {
                    return;
                }

                for (WatchEvent<?> watchEvent : key.pollEvents()) {
                    WatchEvent.Kind<?> eventKind = watchEvent.kind();

                    // 오버플로 이벤트는 무시
                    if (eventKind == OVERFLOW) {
                        continue;
                    }

                    // 이벤트 발생한 파일의 이름을 얻음
                    WatchEvent<Path> ev = (WatchEvent<Path>) watchEvent;
                    Path filename = ev.context();

                    System.out.println(eventKind.name() + ": " + filename);
                }

                //변경 키 초기화, 키를 리셋하지 않으면 더 이상 이벤트를 받지 못함
                boolean valid = key.reset();
                if (!valid) {
                    break;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

<br>


# 2. 디렉터리 변경 감지 활용

디렉터리 변경 감지를 활용하면 여러방법으로 유용하게 사용할 수 있다. 개인적으로 아래 케이스 정도가 프로젝트 시에 개발할 수도 있는 상황 일 듯 하다.

- **파일 시스템 기반 트리거** : 특정 디렉터리에 새로운 파일이 생성되면 이를 모니터링 하다 처리하는 방식으로 WatchService를 사용해 해당 파일이나 디렉터리에 변화가 있을 때마다 특정 작업을 실행할 수 있다. 예를 들어, 새 파일이 특정 디렉터리에 추가될 때 자동으로 처리를 시작하는 시스템 등이 있다.
- **감시 및 보안** : 보안 감시 시스템에서 WatchService를 활용하여 중요한 파일이나 디렉터리에 대한 무단 변경이나 접근을 감지하고, 이를 로깅하거나 경고를 발생시킬 수 있다. 이는 데이터 유출 방지 및 시스템 보안 강화에 도움이 될 수 있다.
- **로깅 및 모니터링** : 시스템 로그 파일 또는 운영 데이터가 지속적으로 기록되는 파일의 변경을 감시하고, 새로운 로그 항목이 추가될 때마다 이를 분석하거나 경고를 발생시키는 시스템에도 WatchService가 유용하다.

<br><br>


---