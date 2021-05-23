---
layout: post
title:  "[Java] XML (2) [#document: null], Document 데이터 XML포맷 형태으로 String으로 출력"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

[#document: null], Document 데이터 XML포맷 형태으로 String으로 출력



<br><br>


# XML 이란?

[#document: null], Document 데이터 XML포맷 형태으로 String으로 출력 뭐라고 포스팅 재목을 적어야 되는지 모르겠다.
Document 데이터를 콘솔에서 출력하면 [#document: null] 로 나온다. 정상적으로 XML 파일 형태로 String으로 반환이 필요 시에.

<br><br>


# 1. XML File 내용 Document로 데이터 변환

```java
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerException;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

import java.io.StringReader;
import java.io.StringWriter;
```

```java
/**
* Document XML to String
* @param doc
* @return
* @throws TransformerException
*/
public static String docToString(Document doc) throws TransformerException {

	TransformerFactory tf = TransformerFactory.newInstance();
	Transformer transformer = tf.newTransformer();
	transformer.setOutputProperty(OutputKeys.OMIT_XML_DECLARATION, "yes");
	StringWriter writer = new StringWriter();
	transformer.transform(new DOMSource(doc), new StreamResult(writer));

	return writer.getBuffer().toString();
}
```

<br><br>

---

[참고]

- https://zetcode.com/java/dom/