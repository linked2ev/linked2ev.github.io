---
layout: post
title:  "[Springboot] 1. Spring boot 프로젝트 생성"
subtitle:   "[Springboot]"
categories: gitlog
tags: springboot
comments: true
---

이번 포스트에서는 스프링부트를 이용한 프로젝트 생성을 진행하겠습니다.


## 1. 스프링부트 소개

스프링부트를 사용하면 실행할 수있는 독립 실행(stand-alone), 제품성 있는 Spring 기반 응용 프로그램을 쉽게 만들 수 있다. 
우리는 스프링 플랫폼과 써드 파티 라이브러리에 대해 의견을 개진하여 최소한의 설정으로 시작할 수 있습니다. 
대부분의 스프링부트 응용 프로그램은 스프링 구성이 거의 필요하지 않다.

## 2. Spring Tool Suite(STS) 소개

우선 Spring Tool Suite란?
스프링 기반의 엔터프라이즈 애플리케이션을 구축하기위한 최고의 Eclipse 기반 개발 환경을 제공하는 개발 툴이다.

하여튼 이번 프로젝트 개발 툴은 STS로 결정했다. [여기](https://spring.io/tools/sts/all)에 가서 다운로드를 받으면 된다.


## 3. 프로젝트 생성

이클립스와 거의 비슷하다.

우클릭 > New > Spring Starter Project
[![프로젝트 생성 STEP1](/assets/img/bmw/201807/2018-07-15-createProjectStep1.png)]()

  
프로젝트명을 명시해주고 나는 빌드는 Gradle로 할 예정이다.  
Maven으로 해도 무방합니다.(추후 Gradle과 Maven에 대한 포스트는 작성 예정)
[![프로젝트 생성 STEP2](/assets/img/bmw/201807/2018-07-15-createProjectStep2.png)]()

  
스프링부트는 프로젝트 생성 시 정말 심플하긴 하다.
자기가 필요한 것은 체크하고 진행하면 프로젝트가 세팅이 된다.
[![프로젝트 생성 STEP3](/assets/img/bmw/201807/2018-07-15-createProjectStep3.png)]()


---
스프링부트에 대해 이미 고견이 있는 고수님들의 블로그들이 많기에
해당 포스트에서는 개인프로젝트 개발 진행과정에 초점을 두겠습니다.