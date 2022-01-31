---
layout: post
title: "[Swagger2] To Using Swagger2 in Springboot + HATEOAS Project"
subtitle: "[Java]"
categories: spring
tags: spring-devhistory
comments: true
---

스프링부트 + HATEOAS 프로젝트에서 Springboot, Gradle, Swagger 버전 변경 시에 에러 발생

Parameter 0 of method linkDiscoverers in org.springframework.hateoas.config.HateoasConfiguration required a single bean, but 17 were found:

<br><br>


# 에러

> 브라우저 alert 메시지

```
Unable to infer base url. This is common when using dynamic servlet registration or when the API is behind an API Gateway. The base url is the root of where all the swagger resources are served. For e.g. if the api is available at http://example.org/api/v2/api-docs then the base url is http://example.org/api/. Please enter the location manually:
```

<br>


> Console Error Log

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of method linkDiscoverers in org.springframework.hateoas.config.HateoasConfiguration required a single bean, but 17 were found:
	- modelBuilderPluginRegistry: defined in null
	- modelPropertyBuilderPluginRegistry: defined in null
	- typeNameProviderPluginRegistry: defined in null
	- syntheticModelProviderPluginRegistry: defined in null
	- documentationPluginRegistry: defined in null
	- apiListingBuilderPluginRegistry: defined in null
	- operationBuilderPluginRegistry: defined in null
	- parameterBuilderPluginRegistry: defined in null
	- expandedParameterBuilderPluginRegistry: defined in null
	- resourceGroupingStrategyRegistry: defined in null
	- operationModelsProviderPluginRegistry: defined in null
	- defaultsProviderPluginRegistry: defined in null
	- pathDecoratorRegistry: defined in null
	- apiListingScannerPluginRegistry: defined in null
	- relProviderPluginRegistry: defined by method 'relProviderPluginRegistry' in class path resource [org/springframework/hateoas/config/HateoasConfiguration.class]
	- linkDiscovererRegistry: defined in null
	- entityLinksPluginRegistry: defined by method 'entityLinksPluginRegistry' in class path resource [org/springframework/hateoas/config/WebMvcEntityLinksConfiguration.class]


Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed
```

<br><br>


# 해결

> SwaggerConfiguration.java

Swagger 설정하는 클래스에 아래 메서드 빈 설정

```java
@Bean
public LinkDiscoverers discoverers(){
    return new LinkDiscoverers(SimplePluginRegistry.create(Arrays.asList(new CollectionJsonLinkDiscoverer())));
}
```

```java
@Configuration
@EnableSwagger2
public class SwaggerConfiguration {

    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(this.apiInfo())
                .select()
                .paths(PathSelectors.ant("/v2/**")) // 경로 패턴 URI만 추출
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Springboot + HATEOAS Project")
                .version("cloud config")
                .description("Neo 프로젝트 Cloud Config Client API 서버")
                .build();
    }

    /**
     * When using Swagger in Springboot + HATEOAS Project, LinkDiscoverers setting is required
     * @return
     */
    @Bean
    public LinkDiscoverers discoverers(){
        return new LinkDiscoverers(SimplePluginRegistry.create(Arrays.asList(new CollectionJsonLinkDiscoverer())));
    }
}
```


--- 

- https://www.baeldung.com/swagger-2-documentation-for-spring-rest-api
- https://stackoverflow.com/questions/58431876/why-hateoas-starts-creating-issue-for-spring-boot-version-2-2-x-during-startu