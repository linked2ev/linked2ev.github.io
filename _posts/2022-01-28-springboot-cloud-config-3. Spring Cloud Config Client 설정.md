---
layout: post
title:  "3. Spring Cloud Client 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-cloudconfig
comments: true
---

`Spring Cloud Client`(어플리케이션)는 Cloud Config 설정파일 정보를 가져와서 서버를 구동한다.

<br><br>


# 1. Spring Config 설정

[![ccc-s3](/assets/img/2022/ccc-s3.png)]() <br>

<br>

> Spring Cloud 의존성

Cloud Config Server와 마찬가지로 Spring Cloud 의존성 추가

```
implementation 'org.springframework.boot:spring-boot-starter-actuator'
implementation 'org.springframework.cloud:spring-cloud-starter-config'
```

<br>

> build.gradle

```gradle
plugins {
    id 'org.springframework.boot' version '2.5.9'
    id 'io.spring.dependency-management' version '1.0.8.RELEASE'
    id 'java'
}

group = 'com.rest'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

//spring cloud 설정
ext {
    springCloudVersion = '2020.0.4'
}

configurations {
    developmentOnly
    runtimeClasspath {
        extendsFrom developmentOnly
    }
    compileOnly {
        extendsFrom annotationProcessor
    }
    all {
        exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.0'
    implementation 'org.springframework.boot:spring-boot-starter-log4j2'
    implementation 'org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4.1:1.16'
    implementation 'org.springframework.boot:spring-boot-starter-hateoas'
    //spring cloud 설정
    implementation 'org.springframework.cloud:spring-cloud-starter-bootstrap'
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.cloud:spring-cloud-starter-config'

    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'

    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    runtimeOnly 'mysql:mysql-connector-java'

    //swagger2
    implementation 'io.springfox:springfox-swagger2:2.9.2'
    implementation 'io.springfox:springfox-swagger-ui:2.9.2'
    implementation 'org.springframework.plugin:spring-plugin-core:1.2.0.RELEASE'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'junit:junit:4.12'
    testImplementation 'org.mockito:mockito-core:2.7.22'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}
```

<br>


> application.yml

Cloud Config Client 역할인 프로젝트에 Config Server 정보를 가져오게 application 설정 정보를 수정

```yml
spring:
  profiles:
    active: dev
  cloud:
    config:
      uri: http://localhost:8888 #Config Server uri
```

<br><br>


# 2. Spring Cloud Client 구동 확인

> Spring Cloud Client 로그

설정파일 변경 후에도 정상적으로 Client 서버는 실행된다. 

```
2022-01-31 14:30:43 [INFO ] [] (NeoApplication                     :676) The following profiles are active: dev
2022-01-31 14:30:45 [DEBUG] [] (HikariConfig                       :971) Driver class net.sf.log4jdbc.sql.jdbcapi.DriverSpy found in Thread context class loader org.springframework.boot.devtools.restart.classloader.RestartClassLoader@32e24a8f
2022-01-31 14:30:45 [DEBUG] [] (HikariConfig                       :1103) HikariPool-1 - configuration:
2022-01-31 14:30:45 [DEBUG] [] (HikariConfig                       :1135) allowPoolSuspension................................false
2022-01-31 14:30:45 [DEBUG] [] (HikariConfig                       :1135) autoCommit................................true
2022-01-31 14:30:45 [DEBUG] [] (HikariConfig                       :1135) catalog................................none
2022-01-31 14:30:45 [DEBUG] [] (HikariConfig                       :1135) connectionInitSql................................none
2022-01-31 14:30:45 [DEBUG] [] (HikariConfig                       :1135) connectionTestQuery................................"SELECT 1"
```

<br>

> Spring Cloud Config Server 로그

```
2022-01-31 14:50:42.155  INFO 91659 --- [nio-8888-exec-3] o.s.c.c.s.e.NativeEnvironmentRepository  : Adding property source: Config resource 'file [/var/folders/jt/xt8g5__92ms_k5xlf0w4l50r0000gn/T/config-repo-6373310396527221640/application-dev.yml]' via location 'file:/var/folders/jt/xt8g5__92ms_k5xlf0w4l50r0000gn/T/config-repo-6373310396527221640/'
```

<br><br>


---
[참고]
- https://spring.io/projects/spring-cloud
- https://github.com/spring-cloud/spring-cloud-release/wiki/Spring-Cloud-2020.0-Release-Notes
- https://docs.microsoft.com/ko-kr/azure/spring-cloud/how-to-prepare-app-deployment?pivots=programming-language-java