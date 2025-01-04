---
layout: post
title:  "[Practical-Java] 23. Java Module"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

java 모듈시스템에 대한 포스팅이며 jdeps 와 jlink 에 대한 단순 기록성 포스팅이다.

<br>

# 1. Java 모듈 시스템

Java 모듈 시스템은 Java 9에서 도입된 주요 기능 중 하나로, Java 플랫폼 모듈 시스템(JPMS)라고도 불립니다. 이 시스템은 Java 애플리케이션의 구조를 개선하고, 더 나은 캡슐화, 유지 관리성 및 성능을 제공하기 위해 설계되었다.

Java 모듈은 관련된 클래스와 리소스 파일, 그리고 이들을 하나로 묶는 모듈 설명자(`module-info.java`)를 포함하는 새로운 프로그래밍 구성 요소이다. 모듈 설명자는 모듈의 이름, 필요한 다른 모듈들, 모듈이 외부로 공개하는 패키지, 서비스 제공 및 사용에 대한 선언 등을 포함한다.

### 모듈의 주요 특징
1. **캡슐화와 정보 은닉**: 모듈은 기본적으로 내부 구현을 외부에 숨기며, 오직 `exports` 선언을 통해 명시적으로 공개된 패키지만 다른 모듈에서 접근할 수 있다.
2. **명시적 의존성**: 모든 모듈은 사용하려는 다른 모듈에 대해 명시적으로 선언해야 하며(`requires` 키워드 사용), 이는 모듈 간의 의존성이 명확하고 관리하기 쉽다.
3. **버전 관리**: 이론적으로 모듈은 버전 정보를 포함할 수 있으며, 이를 통해 보다 세밀한 의존성 관리가 가능하다.
4. **효율성**: 필요한 모듈만 포함하여 애플리케이션을 빌드할 수 있으므로 애플리케이션의 시작 시간과 메모리 사용량이 최적화된다.

### 모듈 설명자 (`module-info.java`)
모듈 설명자는 모듈의 루트 디렉토리에 위치하며 다음과 같은 기본 구조를 가진다:
```java
module 모듈이름 {
    requires 다른모듈1;
    requires 다른모듈2;
    exports 공개할패키지1;
    exports 공개할패키지2;
}
```
예를 들어, 어떤 애플리케이션이 **java.sql** 모듈을 필요로 할 경우, 모듈 설명자에는 `**equires java.sql;**을 포함한다.

### Java 플랫폼의 모듈화
Java 자체도 **java.base**, **java.sql**, **java.xml** 등 다양한 표준 모듈로 나뉘어져 있어, 애플리케이션에 필요한 부분만 선택하여 사용할 수 있다.

Java 모듈 시스템은 큰 프로젝트에서 의존성을 더 잘 관리하고, 애플리케이션을 보다 안전하고 유지보수하기 쉽게 만들어준다. 모듈 시스템의 도입은 특히 대규모 애플리케이션 또는 많은 외부 라이브러리에 의존하는 프로젝트에 유용하다.

<br>

예시로 공급자모듈, 서비스모듈, 소비자모듈 구조인 예시이다.

공급자모듈 : 서비스 인터페이스를 구현한 클래스들이 위치한 모듈이다. 경우에 따라서 서비스 모듈을 포함시킬 수 있다. 모듈 패스에 서비스 모듈이 포함되어 있어야 한다.  
서비스모듈 : 서비스 인터페이스들을 모아 놓은 모듈이다. 모듈이 세분화되는 것을 원하지 않는다면 공급자 모듈에 포함시킬 수도 있다.  
소비자모듈 : 서비스 모듈을 소비하는 모듈이다. 모듈에 서비스 모듈과 공급자 모듈이 모두 포함되어 있어야 한다.  

<br>

> module-info.java

```js
module module.sample.provider {
    // service_registrant 패키지를 외부에 노출시킨다.
    exports sample_module.service_registrant;

    // InsightService 를 구현한 2개의 클래스를 지정한다.
    provides sample_module.service_registrant.SampleService with // <서비스하는 인터페이스> with <구현, 상속 받은 클래스 구현체>
            sample_module.service_provider.SampleServiceProviderA,
            sample_module.service_provider.SampleServiceProviderB;
}
```

<br>

> module-info.java

```js
module module.sample.consumer {
    //모듈 선언
    exports sample_module.service_consumer;
    //sample_provider_module
    requires module.sample.provider;
    //아래와 같이 선언해야 ConsumerModule 에서 SampleService 를 사용하겠다는 선언
    uses sample_module.service_registrant.SampleService;
}
```

<br>

> ConsumerModule.java

```java
package sample_module.service_consumer;

import sample_module.service_registrant.SampleService;
import java.util.ServiceLoader;

public class ConsumerModule {

    public static void main(String[] args) {
        //서비스 목록을 가져온다.
        ServiceLoader<SampleService> loader = ServiceLoader.load(SampleService.class);

        for (final SampleService service : loader) {
            System.out.println("# 서비스명 : " + service.getClass().getSimpleName() + ", 실행 : " + service.echo(service.getClass().getSimpleName()));
        }
    }
}
```

<br><br>


# 2. jdeps

`jdeps`는 Java Dependency Analysis Tool이다. 이 도구는 Java 클래스 파일, JAR 아카이브, 또는 모듈에 대한 종속성을 분석하고, 사용된 패키지와 클래스 간의 종속성 정보를 제공한다. Java 8 이상에서 사용할 수 있으며, 특히 Java 모듈 시스템에 대해 도입된 Java 9 이후 버전에서 중요한 역할을 한다.

- **의존성 분석**: 클래스나 패키지가 다른 클래스, 패키지, 또는 모듈에 어떻게 의존하는지를 보여 준다.
- **모듈 경로 분석**: 모듈 경로 상에 있는 모듈들 사이의 의존성을 분석한다.
- **API 사용 분석**: 애플리케이션이 사용하는 JDK 내장 API들을 보여 준다.

<br>

#### jdeps 명령어

```bash
jdeps [options] <jar 파일 또는 클래스 파일>
```

#### 주요 options

- `-verbose`: 클래스 수준에서의 의존성을 보여 준다.
- `-summary`: 요약된 의존성 정보를 제공한다.
- `-recursive`: 지정된 클래스나 패키지의 의존성을 재귀적으로 분석한다.
- `-jdkinternals`: JDK 내부 API 사용을 분석하여, 이후 릴리스에서 사용이 제한될 수 있는 API를 식별한다.

<br>


## 2-1. jdeps 사용 예시

### 1) 단일 JAR 파일의 경우
만약 "JavaModule"이라는 이름의 JAR 파일이 있다면, 해당 파일의 종속성을 분석해서 `JavaModule.jar` 파일 내의 모든 클래스에 대한 자세한 종속성 정보를 출력한다.

```bash
jdeps -v JavaModule.jar
```

<br>

### 2) 여러 JAR 파일이 있는 프로젝트의 경우
프로젝트에 여러 JAR 파일이 포함되어 있고, 이들 간의 종속성을 분석하고 싶다면, 각각의 JAR 파일에 대해 `jdeps`를 실행하거나, 모든 JAR 파일을 포함하는 디렉토리를 지정할 수 있다.

```bash
jdeps -v /path/to/project/libs/*.jar
```

<br>

### 3) Java 모듈의 경우
"JavaModule"이라는 이름의 Java 모듈이 있다면, 해당 모듈의 종속성을 분석하기 위해 모듈 경로와 모듈 이름을 명시적으로 지정할 필요가 있다.  
여기서 `--module-path`는 모듈이 위치한 디렉토리를 가리키며, `--module`은 분석할 모듈의 이름을 지정한다.

```bash
jdeps --module-path /path/to/modules --module JavaModule
```

<br>

### 4) JDK 내부 API 사용 검사
"JavaModule" 프로젝트가 JDK 내부 API를 사용하고 있는지 확인하려면 다음과 같은 명령을 사용할 수 있습니다:

```bash
jdeps --jdk-internals /path/to/JavaModule.jar
```
이 명령은 JAR 파일이나 모듈이 사용하는 내부적인 JDK API를 보여준다.

<br>


`jdeps` 명령어를 통해서 module.sample.consumer 의 연관된 모듈 정보를 확인 할 수 있다. 진행해보자.

```bash
$ production % jdeps --module-path . -s --module module.sample.consumer

module.sample.consumer -> java.base
module.sample.consumer -> module.sample.provider
```

<br><br>


# 3. jlink

jlink는 Java 모듈 시스템을 활용하여 작은 크기의 커스텀 런타임 이미지를 생성하는 도구로 이 도구는 Java 9 이상에서 사용할 수 있으며, 애플리케이션에 필요한 Java 모듈만 포함시켜 최적화된 런타임 이미지를 만들 수 있게 해주기에 애플리케이션의 배포 크기를 줄이고 성능을 향상시킬 수 있다.

<br>

## 3-1. jlink 이미지 생성

`jlink` 명령어를 통해서 이미지를 만들어보자.

1. **경로 구분자 수정**: Mac 또는 Linux에서 경로 구분자로 콜론(`:`)을 사용하며, Windows의 세미콜론(`;`) 대신 콜론을 사용한다.
2. **환경 변수 확장**: Mac 또는 Linux에서 `$JAVA_HOME`방식을 사용하며, Windows에서는 `%JAVA_HOME%` 사용한다.

```bash
$ production % jlink --module-path .:%JAVA_HOME/jmodes --add-modules module.sample.consumer --output customjre
```

- `--module-path`: 모듈 경로를 지정합니다. 이 경로에는 필요한 모든 모듈과 Java의 기본 모듈이 포함되어야 합니다. 여기서는 `module.sample.consumer` 디렉토리와 Java 홈 디렉토리 내의 `jmods` 디렉토리를 포함
- `--add-modules`: 패키징할 모듈을 지정합니다. 이 예에서는 `module.sample.consumer` 모듈을 추가
- `--output`: 생성될 사용자 정의 JRE가 저장될 디렉토리를 지정

<br>

## 3-2. 생성 된 이미지 실행

생성 된 커스텀jre를 통해서 실행할려면 customjre 폴더 내의 Java 런타임을 사용해 실행해보자 

Java 모듈 시스템에서 --module 옵션을 사용하여 애플리케이션을 실행할 때는 메인 클래스의 경로를 파일 시스템의 상대 경로나 절대 경로로 지정할 수 없으며 모듈 시스템에서 애플리케이션을 실행하려면 
`java --module-path [모듈 경로] --module [모듈명]/[메인 클래스의 전체 패키지 이름]` 해당 명령어로 통해 모듈 시스템에서 Java 애플리케이션이 모듈 기반 애플리케이션 실행할 수 있다.

```bash
$ production % ./customjre/bin/java --module module.sample.consumer/sample_module.service_consumer.ConsumerModule

# 서비스명 : SampleServiceProviderA, 실행 : ServiceProviderA : SampleServiceProviderA
# 서비스명 : SampleServiceProviderB, 실행 : ServiceProviderB : SampleServiceProviderB
```

<br>

 예를 들어, MyProject 디렉토리 내에 app.jar 파일이 있다고 가정하면 커스텀jre를 통해서 다음과 같이 실행할 수 있기에 활용 할 수 있다.

 ```bash
 ./customjre/bin/java -jar ../MyProject/app.jar
 ```

 <br><br>