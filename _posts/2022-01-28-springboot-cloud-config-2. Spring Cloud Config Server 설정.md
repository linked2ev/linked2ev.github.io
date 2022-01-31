---
layout: post
title:  "2. Spring Cloud Config Server 설정"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-cloudconfig
comments: true
---

`Spring Cloud Config Server`는 Cloud Config 설정파일을 불러와서 배포하는 역할을 한다.

<br><br>


# 1. Spring Cloud Config Server란?

[![ccc-s2](/assets/img/2022/ccc-s2.png)]() <br>

<br>

Cloud Config 설정파일을 불러와서 배포하는 역할이다.  
Springboot에서 Cloud Config 모듈을 새로 만들자

<br><br>


# 2. Spring Cloud Config Server 생성

[![ccs-s1](/assets/img/2022/ccs-s1.png)]() <br>

[![ccs-s2](/assets/img/2022/ccs-s2.png)]() <br>

[![ccs-s3](/assets/img/2022/ccs-s3.png)]() <br>

[![ccs-s4](/assets/img/2022/ccs-s4.png)]() <br>

<br><br>


# 3. Config Server Application 설정

> ConfigServerApplication.java

해당 Springboot App이 Cloud Config Server 역할을 한다는 것을 명시 `@EnableConfigServer`

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }

}
```

<br><br>


> build.gradle

```js
implementation 'org.springframework.cloud:spring-cloud-config-server'
```

spring-cloud-config-server 의존성 주입

<br>

```js
plugins {
    id 'org.springframework.boot' version '2.6.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
    id 'war'
}

group = 'spring-cloud'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
    mavenCentral()
}

ext {
    set('springCloudVersion', "2021.0.0")
}

dependencies {
    implementation 'org.springframework.cloud:spring-cloud-config-server'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}

tasks.named('test') {
    useJUnitPlatform()
}
```

<br><br>


> application.yml

이전 포스팅에서 외부에 설정한 설정파일 경로를 설정한다.

```yml
server:
  port: 8888

spring:
  cloud:
    config:
      server:
        git:
          default-label: master
          uri: https://github.com/linked2ev/neo-spring-cloud-config
          #username: private 경우
          #password: private 경우
```

<br><br>


# 4. Spring Cloud Config Server 구동

아래와 같이 서버 구동 시에 Config 자원을 가져와서 설정하는 로그가 올라온다.

```
o.s.c.c.s.e.NativeEnvironmentRepository  : Adding property source: Config resource 'file [/var/folders/jt/xt8g5__92ms_k5xlf0w4l50r0000gn/T/config-repo-6373310396527221640/application-local.yml]' via location 'file:/var/folders/jt/xt8g5__92ms_k5xlf0w4l50r0000gn/T/config-repo-6373310396527221640/'
```

<br><br>

# 5. Cloud Config 확인

방금 만든 Spring Cloud Config Server 프로젝트를 실행 한 다음에  
http://localhost:8888/cloud-config/local 로 접근하면 아래와 같이 설정파일 내용이 json 형태로 출력된다.


```json
{
   "name":"cloud-config",
   "profiles":[
      "local"
   ],
   "label":null,
   "version":"1c008a86f383bd4cb76179052648ccc87cd7f3fd",
   "state":null,
   "propertySources":[
      {
         "name":"https://github.com/linked2ev/neo-spring-cloud-config/application-local.yml",
         "source":{
            "server.port":8080,
            "spring.config.activate.on-profile":"local",
            "spring.datasource.hikari.connection-test-query":"SELECT 1",
            "spring.datasource.hikari.allow-pool-suspesion":true,
            "spring.datasource.hikari.driver-class-name":"net.sf.log4jdbc.sql.jdbcapi.DriverSpy",
            "spring.datasource.hikari.jdbc-url":"jdbc:log4jdbc:mysql://localhost:3306/demo_neo?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC",
            "spring.datasource.hikari.username":"root",
            "spring.datasource.hikari.password":1111,
            "spring.thymeleaf.cache":false,
            "spring.resources.cache.period":0,
            "spring.jpa.database":"mysql",
            "spring.jpa.database-platform":"org.hibernate.dialect.MySQL5InnoDBDialect",
            "spring.jpa.generate-ddl":true,
            "spring.jpa.hibernate.use-new-id-generator-mappings":false,
            "mybatis.configuration.map-underscore-to-camel-case":true
         }
      }
   ]
}
```

<br><br>

이상 끝. 이어서.