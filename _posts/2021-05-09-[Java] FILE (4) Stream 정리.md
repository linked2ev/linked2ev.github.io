---
layout: post
title:  "[Java] FILE (4) Stream 정리"
subtitle:   "[Java]"
categories: java
tags: java-ch-file
comments: true
---

스트림에 대한 정리 (4)

<br>


# 1 Stream(스트림)

데이터를 어떤 방식으로 전달하느냐에 따라 스트림은 두가지

<br>

## 바이트 (Byte) - Input/Output(I/O)

- InputStream -> FileInputStream -> BufferedInputStream (입력)
- OutStream -> FileOutputStream -> BufferedOutputStream (출력)

## 문자 (Charater) - Reader/Writer

- Reader -> FileReader -> BufferedReader (입력)
- Writer -> FileWriter -> BufferedWriter (출력)

<br>

# 1-1 기반 스트림

# 1-1-1 바이트 스트림(Byte Stream)

- `Binary(1 byte) 데이터`를 입출력하는데 사용하는 스트림
- `이미지 파일, 동영상 파일, 음악 파일, 텍스트 파일` 등을 처리할 때 사용되는 스트림
- InputStream / OutputStream 바이트기반 스트림 최상위 클래스
- FileInputStream과 FileOutputStream는 바이트 기반의 파일을 입출력 하는 클래스

<br>


## InputStream / OutputStream 클래스

`InputStream과 OutputStream은 바이트 기반 입력 스트림의 최상위 클래스(조상)로 추상 클래스입니다. 모든 바이트 기반 입/출력 스트림은 이 클래스를 상속`

<br><br>


# 1-1-2 문자 스트림(Character Stream)

- `문자 기반(Character) 데이터`를 입출력하는데 사용하는 스트림
- `HTML 문서, 텍스트 파일` 등을 처리할 때 사용되는 스트림
- Reader / Writer 문자기반 스트림 최상위 클래스
- FileReader와 FileWriter는 문자 기반의 파일을 입출력 하는 클래스

<br>

## Reader / Writer 클래스

`Reader과 Writer는 문자 기반 입력 스트림의 최상위 클래스(조상)로 추상 클래스입니다. 모든 문자 기반 입/출력 스트림은 이 클래스를 상속`

<br><br>


# 1-2 보조 스트림

스트림의 기능을 보완하기 위한 보조 스트림이 제공된다. 보조 스트림은 실제 데이터를 주고받는 스트림이 아니기 때문에 데이터를 입출력할 수 있는 기능을 없지만. `스트림의 기능을 향상시키거나 새로운 기능을 추가`할 수 있다.

<br>

# 1-2-1 바이트 보조 스트림

## 1) FilterInputStream / FilterOutputStream 클래스

FilterInputStream과 FilterOutputStream은 `InputStream/OutputStream의 자손이면서 모든 보조 스트림의 조상`이며,
보조 스트림은 자체적으로 입출력을 수행할 수 없기 때문에 기반 스트림을 필요로 한다.

<br>

## 2) BufferedInputStream / BufferedOutputStream 클래스

`한 바이트씩 입출력하는 것 보다 버퍼(바이트배열)를 사용해서 한 번에 여러 바이트를 입출력하는 것이 빠르기 때문에 대부분의 입출력 작업에 사용`되는 버퍼를 이용한 보조 스트림이다.

<br>

# 1-2-2 문자기반 보조 스트림

<br>

# 1) BuffredReader와 BufferedWriter

`BuffredReader와 BufferedWriter는 버퍼를 이용한 문자 기반 입출력의 효율`을` 높일 수 있도록 해주는 역할을 스트림이다.

<br>

# 2) InputStreamReader와 OutputStreamWriter

`바이트 기반 스트림을 문자 기반 스트림으로 연결시켜주는 역할`을 한다. 그리고 바이트기반 스트림의 데이터를 지정된 인코드의 문자 데이터로 변환하는 작업을 수행한다.


<br><br>


