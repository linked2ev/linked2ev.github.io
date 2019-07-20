---
layout: post
title:  "[DB] WorkBench - Lost connection to MySQL server during query"
subtitle:   "[DB]"
categories: database
tags: dbtool-sub
comments: true
---

WorkBench - Error Code: 2013. Lost connection to MySQL server during query

<br>

## 이슈
---

- WorkBench DB 툴에서 select시 타임 아웃 에러 발생
- `Error Code: 2013. Lost connection to MySQL server during query`

<br>

## 해결
---

- WorkBench 내에 설정을 통해 해결
- Edit > Preferences > SQL Editor > DBMS connection read time out (in seconds): 6000 (해당 값을 높여준다.)