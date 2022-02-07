---
layout: post
title:  "4. Cloud Config 동적 변경 및 yml 파일 정보 가져오기"
subtitle:   "[springboot]"
categories: gitlog
tags: springboot-cloudconfig
comments: true
---

Cloud Config 동적 변경 및 yml 파일 정보 가져오기 위한 포스팅이다.

<br><br>


# 1. Cloud Config 설정 yml 파일 정보 가져오기
---

> build.gradle

이전에 아래에 추가한 의존성의해 동적변경이 가능하다.

```gradle
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

<br><br>


> application-dev.yml

Github Repository 에 만들어 놓은 Spring Cloud Config 외부설정파일에 아래와 같이 테스트 하기 위해 추가 작성해준다.

```yml
const:
  profile: dev
  test-key: 1111
```

<br><br>


> CloudConfigService.java

profile 설정파일 정보를 가져오기 위해서 설정정보를 관리(확인하기위해)하는 클래스를 생성해주고  
`변경된 설정파일의 설정이 애플리케이션의 재배포 없이 실시간으로 동적으로 반영하기 위해서` 
아래와 같이 어노테이션을 선언한다.

```java
@RefreshScope
```

```java
@Slf4j
@Configuration
@RefreshScope
public class CloudConfigService {

    @Value("${const.profile}")
    private String profile;

    @Value("${const.test-key}")
    private String testKey;

    @Bean
    public void profileConfig() {
        this.getConfig();
    }

    public Map<String, Object> getConfig() {
        Map<String, Object> configMap = new HashMap<>();
        log.info("==> config-info: profile={}, testKey={}", profile, testKey);

        configMap.put("profile", profile);
        configMap.put("testKey", testKey);

        return configMap;
    }

}
```

<br><br>


> ConfigApiController.java

yml파일을 수정했을 떄 profile 정보를 동적으로 수정된 정보를 가져오는 역할을 하는  
Controller를 생성

```java
@Slf4j
@RestController
@RequestMapping("/v2/api/common")
public class ConfigApiController {

    @Autowired
    public CloudConfigService cloudConfigService;

    @GetMapping
    public void getConfig(HttpServletRequest request) throws Exception {
        cloudConfigService.getConfig();
    }
}
```

<br><br>


## yml 파일 정보 가져오기

위와 같이 설정한 후에 서버 구동 및 localhost:8080/v2/api/common 호출 시에 아래와 같이
yml 파일 설정 정보를 가져오는것을 확인 할 수 있다.

```
[INFO ] [] (CloudConfigService                 :32 ) ==> config-info: profile=dev, testKey=1111
```

<br><br>


# 2. Cloud Config 동적 변경 확인
---

이제 동적으로 변경되는거를 테스트 하기 위해서 Github Repository 에 만들어둔 설정파일 내용을 수정하고

```yml
const:
  profile: dev
  test-key: 2222
```

<br>

설정파일을 수정한다고 저절로 어플리케이션에 동적으로 설정정보가 변하는게 아니다. 어플리케이션 내에 있는 정보를 POST요청을 수정하는 개념이다.

아래 curl 명령어로 `Config Client 서버에 POST요청`을 해보자. 

```
$ curl -X POST "http://localhost:8080/actuator/refresh"
```

<br>

하여튼 POST 명령 후에 서버 재시작 없이 localhost:8080/v2/api/common 호출하면 `@RefreshScope` 선언에 의해 아래와 같이 test-key 값이 2222가 변한걸 볼 수 있다.

```
[INFO ] [] (CloudConfigService                 :32 ) ==> config-info: profile=dev, testKey=2222
```

POST 요청 없이 바로 업데이트 되도록 하는 Spring Cloud Bus를 조만간 작성예정.

<br><br>


---