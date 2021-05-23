---
layout: post
title:  "[Java] XML (1) XML File 내용 Document로 데이터 변환 및 Document 사용하기"
subtitle:   "[Java]"
categories: java
tags: java-devhistory
comments: true
---

XML File 내용 Document로 데이터 변환 및 Document 기본적인 예제 대한 포스팅이다.



<br><br>


# XML 이란?

- [참고] https://linked2ev.github.io/devlog/2021/05/19/WEV-DOM(Document-Object-Model)-%EA%B5%AC%EC%A1%B0%EB%A5%BC-%EA%B0%96%EB%8A%94-XML(EXtensible-Markup-Language)/

<br><br>


# 1. XML File 내용 Document로 데이터 변환

```java
/**
* XML 형태의 String을 Document 로 반환
* @param XMLString
* @return Document
*/
public static Document XMLString2Doc(String XMLString) throws Exception {

	InputSource is = new InputSource(new StringReader(XMLString));
	is.setEncoding("UTF-8");
	DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
	DocumentBuilder dBuilder = dbFactory.newDocumentBuilder();

	return dBuilder.parse(is);
}

/**
* XML 파일 경로로 Document 로 반환
* @param filePath
* @return
* @throws Exception
*/
public static Document XMLFilePath2Doc(String filePath) throws Exception {

	DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
	DocumentBuilder dBuilder = dbFactory.newDocumentBuilder();

	return dBuilder.parse(filePath);
}
```

<br><br>


# 2. Java 에서 XML Document 사용

<br>

## 2-1. Document List Node명으로 Read(가져오기)

> XML 파일(데이터 구조)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<empList>
    <emp>
        <id>E0010</id>
        <name>김정우</name>
        <deptId>110</deptId>
    </emp>
    <emp>
        <id>E0020</id>
        <name>이정민</name>
        <deptId>120</deptId>
    </emp>
    <emp>
        <id>E0030</id>
        <name>박혜민</name>
        <deptId>210</deptId>
    </emp>
</empList>
```

<br>

> Main.java

```java
public class XmlServiceMain {

    public static void main(String[] args) throws Exception {

        try {
            Document doc = XMLUtil.XMLFilePath2Doc("src/main/resources/xmlData/empList.xml");
            XMLUtil.getNodeList(doc, "emp");//empList.xml
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

<br>

> XMLUtil.java

```java
/**
* Doc 내에 노드 list 가져오기
* @param doc
* @param getNodeList
*/
public static void getNodeList(Document doc, String getNodeList) {
	doc.getDocumentElement().normalize();

	String nm = doc.getDocumentElement().getNodeName();
	System.out.println("Root element :" + doc.getDocumentElement().getNodeName());
	NodeList nList = doc.getElementsByTagName(getNodeList);

	travNode(nList);
}
//노드 순회 메서드
public static void travNode (NodeList nodes) {

	for (int i = 0; i < nodes.getLength(); i++) {
		Node n = nodes.item(i);

		if (n.getNodeType() == Node.ELEMENT_NODE) {
			NodeList childNodes = n.getChildNodes();
			int cnt = childNodes.getLength();

			if (cnt == 1) {
				System.out.printf("%s : %s%n", n.getNodeName(), n.getTextContent());
			} else {
				System.out.printf("--------[ " + n.getNodeName() + " ]--------%n");
			}

			if (cnt > 1) {
				travNode(childNodes);
			}
		}
	}
}
```

<br>

> 결과

```
Root element :empList
--------[ emp ]--------
id : E0010
name : 김정우
deptId : 110
--------[ emp ]--------
id : E0020
name : 이정민
deptId : 120
--------[ emp ]--------
id : E0030
name : 박혜민
deptId : 210
```


<br><br>


## 2-2. Document Node 순회하면서 Read(가져오기)

> XML 파일(데이터 구조)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<dept>
    <tech>
        <design>
            <id>110</id>
            <name>디자인팀</name>
        </design>
        <publisher>
            <id>120</id>
            <name>퍼블리셔팀</name>
        </publisher>
        <develop>
            <id>130</id>
            <name>개발팀</name>
        </develop>
    </tech>
    <support>
        <management>
            <id>210</id>
            <name>경영팀팀</name>
        </management>
        <sales>
            <id>220</id>
            <name>영업팀</name>
        </sales>
    </support>
</dept>
```

<br>

> Main.java

```java
public class XmlServiceMain {

    public static void main(String[] args) throws Exception {

        try {
            Document doc = XMLUtil.XMLFilePath2Doc("src/main/resources/xmlData/depts.xml");
            XMLUtil.getTraversalNodeList(doc);//depts.xml
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

<br>

> XMLUtil.java

```java
/**
* 모든 노드 순회 가져오기
* @param doc
*/
public static void getTraversalNodeList (Document doc) {
	//Doc 순회
	DocumentTraversal trav = (DocumentTraversal) doc;
	DocFilter docFilter = new DocFilter("params");//조건에 따른 필터 처리

	NodeIterator it = trav.createNodeIterator(doc.getDocumentElement(),
			NodeFilter.SHOW_ELEMENT, docFilter, true);

	for (Node n = it.nextNode(); n != null; n = it.nextNode()) {
		int cnt = n.getChildNodes().getLength();

		if (cnt == 1) {
			System.out.printf("%s : %s%n", n.getNodeName(), n.getTextContent());
		} else {
			System.out.printf("--------[ " + n.getNodeName() + " ]--------%n");
		}
	}
}
//DocumentTraversal 필터
static class DocFilter implements NodeFilter {

	String deptId = "";

	public DocFilter (String deptId) {
		this.deptId = deptId;
	}

	@Override
	public short acceptNode(Node thisNode) {

		if (thisNode.getNodeType() == Node.ELEMENT_NODE) {
			Element e = (Element) thisNode;
			String nodeName = e.getNodeName();

			if (! "params".equals(this.deptId)) {
				return NodeFilter.FILTER_REJECT;
			}
		}

		return NodeFilter.FILTER_ACCEPT;// NodeFilter.FILTER_REJECT;
	}
}
```

<br>

> 결과

```
--------[ dept ]--------
--------[ tech ]--------
--------[ design ]--------
id : 110
name : 디자인팀
--------[ publisher ]--------
id : 120
name : 퍼블리셔팀
--------[ develop ]--------
id : 130
name : 개발팀
--------[ support ]--------
--------[ management ]--------
id : 210
name : 경영팀팀
--------[ sales ]--------
id : 220
name : 영업팀
```

<br><br>


---

[참고]

- https://zetcode.com/java/dom/