---
layout: post
title:  "[Nuxt][Chapter1] 1. Nuxt Admin Template Intro"
subtitle:   "[Nuxt]"
categories: devlog
tags: nuxt
comments: true
---

`[ Nuxt Admin Template > Chapter1 ]`  


현재 Nuxt 포스팅부터는 Nuxt로 Admin Template 만드는 과정을 기록하기 위한 포스팅입니다.

<br>

## 1-1. Nuxt Admin Template Intro
---

> 이번 포스팅부터는 티스토리에 같이 연재합니다.<br>  
우선 필자는 Front-end 전문 개발자가 아니고 연차도 많지 않은 자바 개발자입니다. 그래서 포스팅 내용이 틀리거나 개발방법이 비효율적이거나 더 좋은 방법론이 있을수도 있습니다.<br>  
개발 진행하면서 Nuxt에 공식 사이트나 커뮤니티에서 필요한 내용만 메모하고 정리하는 포스팅을 진행하기 때문에 Nuxt에 관련된 문서는 공식 사이트에서 참고하시기 바랍니다.

- [nuxt.js github](https://github.com/nuxt/nuxt.js)
- [nuxt.js ko guide](https://ko.nuxtjs.org/guide/)
- [nuxt.js ko api](https://ko.nuxtjs.org/api)
- [nuxt.js community](https://github.com/nuxt-community)

<br>

### Nuxt Admin Template 개발에 앞서

- vuetifyjs에서 제공하는 [Nuxt.js + Vuetify.js Template](https://github.com/vuetifyjs/nuxt)으로 진행   
- 개발진행과 포스팅은 별개이기에 내용이 상이 할 수 있음
- Chapter1에서 다룰 내용은 크게 login, Auth와 jwt

<br>

## 1-2. vuetifyjs/nuxt template create app
--- 

새로운 Nuxt.js 프로젝트를 vuetifyjs/nuxt template으로 생성합니다.

> cmd

```
$ vue init vuetifyjs/nuxt <my-project>  
$ cd <my-project>                     
# install dependencies
$ npm install # Or yarn install
$ npm run dev
```

<br>

---
참고

+ [nuxt.js github](https://github.com/nuxt/nuxt.js)
+ [nuxt.js ko guide](https://ko.nuxtjs.org/guide/)
+ [nuxt.js ko api](https://ko.nuxtjs.org/api)
+ [nuxt.js community](https://github.com/nuxt-community)
