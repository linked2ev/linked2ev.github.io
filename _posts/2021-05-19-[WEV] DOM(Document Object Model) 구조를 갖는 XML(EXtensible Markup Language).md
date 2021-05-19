---
layout: post
title:  "[WEB] DOM(Document Object Model) 구조를 갖는 XML(EXtensible Markup Language)"
subtitle:   "[WEB]"
categories: devlog
tags: web
comments: true
---

이기종 간에 데이터를 전달, 교환하기 위해서 DOM(Document Object Model) 구조를 갖는 XML(EXtensible Markup Language)을 사용한다,

<br><br>


# 1. XML 이란?

XML은 EXtensible Markup Language의 약자이며, 1998년에 W3C 표준 권고 포함되었다. `XML은 데이터를 저장하고 전달할 목적`으로 만들어졌으며, 저장되는 `데이터의 구조를 기술`하기 위한 언어이며, 수많은 응용 분야에서 데이터를 저장하고 전달하는 중요한 역할을 맡고 있다.

<br>


# 2. XML의 특징

1. 다른 목적의 마크업 언어를 만드는 데 사용되는 `다목적 마크업 언어`입니다.
2. 이기종끼리 `데이터 교환`이 용이하다.
3. 새로운 태그를 만들어 추가해도 계속해서 동작하므로, `확장성`이 좋습니다.
4. 데이터를 보여주지 않고, `데이터를 전달하고 저장`하는 것만을 목적으로 합니다.
5. `텍스트(문자) 데이터 형식`의 언어로 모든 XML 문서는 유니코드 문자로만 이루어집니다.
6. HTML과 같이 문서 구조를 트리 형태로 하여 `객체화할 수 있는 DOM(Document Object Model) 구조`이다

<br>

XML은 범용성과 확장성으로 인해 이기종간에 데이터 교환이 용이하며 유연하다. 또한 [네임스페이스](http://tcpschool.com/xml/xml_basic_namespace) 와 [XML 파서(parser)](http://tcpschool.com/xml/xml_basic_document) 로 문서 검증로 인해 기간계 등에서 특정 서비스에서 사용하는 듯하다.

하지만, json 과 비교시에 가독성이 낮으며, 데이터 파싱도 번거롭다. 처리속도도 느리다. 괜히 json 데이터 구조를 원하는게 아니다.


<br><br>


## 3. XML DOM 구조 
 
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- Element (요소) -->
<programming_languages>
    <language>
        <name>HTML</name>
        <category>web</category>
        <developer>W3C</developer>
        <priority rating="1">high</priority>
    </language>
    <language>
        <name>CSS</name>
        <category>web</category>
        <developer>W3C</developer>
        <priority rating="3">middle</priority>
    </language>
    <language>
        <name korean="자바">Java</name>
        <category>application</category>
        <developer>Oracle</developer>
        <priority rating="2">high</priority>
    </language>
</programming_languages>
```

<br>


## 3-1. XML Declaration (XML 문서의 선언)

```xml
<?xml  version="1.0"  encoding="UTF-8" standalone="no"?>
```

- encoding
지정하지 않으면 "UTF-16" 또는 "UTF-8"로 해석 (영문 코드)
한글의 경우 EUC-KR 또는 KSC5601

- standalone : 속성값은 "yes" 또는 "no"
    - "yes" : 기본값, 외부파일(그림, 개체, 엔티티 등)에 대한 참조 없다.
    - "no" : 외부파일의 참조가 있을 수 있다. (단지 파서에게  알려만 준다.)
생략하는 경우가 많다. 이 경우 파서가 해석하여 판단한다.

<br>

## 3-2. Element (요소)

```xml
<요소명>Element DATA</요소명>
```
- XML의 기본단위
- 시작 태그와 끝 태그가 반드시 짝을 이루어야 한다.
- Element DATA 는 Parsed Character Data (PCDATA) 형식
- Root element (루트 요소) : 문서의 최상위 요소

<br>

> 요소명 명명규칙

요소명의 첫 문자는 문자, _(언더스코어)로만 시작해야 되며 요소명에는 "xml"는 사용이 불가능하다.

<br><br>


## 3-3. DTD & Entity

Entity Reference (엔티티 참조, 개체 참조)
- 일종의 escape 문자 : &엔티티명;
- 사용 순서
    - 선언(declaration) : 시스템 (특수문자) 또는 사용자 정의
    - 참조(reference) : 문서 작성시
    - 대체(replacement) : 문서 해석시

<br>

> 사용자 정의 예 DTD에서 선언

```xml
<!ENTITY cate "IT">
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<KRX>
    <stock>
        <code>KRX:005930</code>
        <name>삼성전자</name>
        <category>IT</category>
        <price>78500</price>
    </stock>
</KRX>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!ENTITY cate "IT">

<KRX>
    <stock>
        <code>KRX:005930</code>
        <name>삼성전자</name>
        <category>&cate;</category>
        <price>78500</price>
    </stock>
</KRX>
```

<br>

> 결과

```xml
<category>IT</category>
```

<br><br>


## 3-4. XML 속성(Attribute)

속성은 중첩 염려 없고 순서 상관 없으며 텍스트 이외의 데이터 형식를 지정할 수 있다.

- Element와 결합된 이름="값"의 쌍으로 시작태그에만 지정
- 속성 명과 속성 값이 하나의 쌍 (속성값은 반드시 존재)
- 속성값은 큰따옴표나 작은따옴표로 감싼다.
- 속성이 여러 개 일 때 공백으로 분리, 순서는 무의미, 동일 속성은 한번만 존재
- 속성명 규칙은 요소명 규칙과 동일

<br>

> 속성 표현 예시

```xml
<stock>
	<code>KRX:005930</code>
	<name>삼성전자</name>
	<category>IT</category>
	<price>78500</price>
</stock>

<!-- 속성 표현 -->
<stock code="KRX:005930" name="삼성전자" category="IT" price="78500">
</stock>
```

```xml
<icon img="stock_icon.gif"></icon>
<email href="linked2ev@mail.com" >linked2ev@mail.com</email>
```

<br><br>


## 3-5. CDATA

CDATA section : \<![CDATA[ ...데이터... ]]>

<br>

\<![CDATA[]]> 에는 HTML 태그표현, 특수기호(<, ", '), 바이너리 데이터 등을 데이터 그대로 저장 및 전달하고자 할때 사용된다.


```xml
<string>&lt;P&gt;XML 데이터&lt;/P&gt;</string>
```

```xml
<string><![CDATA[<P>XML 데이터</P>]]></string>
```

<br><br>

---
[참고]

- https://www.w3schools.com/xml/
- http://www.tcpschool.com/xml/intro
- https://jae-jae.tistory.com/187

