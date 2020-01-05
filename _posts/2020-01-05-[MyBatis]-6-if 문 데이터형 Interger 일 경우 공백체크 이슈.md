---
layout: post
title:  "[MyBatis] if 문 데이터형 Interger 일 경우 공백체크 이슈"
subtitle:   "[Mybatis]"
categories: mybatis
tags: mybatis-posting
comments: true
---

Mybatis 동적쿼리 if 문 사용시 파라미터 데이터형이 Interger 일 경우 공백체크 이슈

<br>


# 이슈

> Goods.java

```java
@Data
public class Goods implements Serializable  {

    public Integer goodsIdx;
    //...생략...
}
```

> SQL Mapper

```xml
<select id="selectGoods" parameterType="Goods" resultType="Goods">
    SELECT
        *
    FROM GOODS_OPTION
    <where>
        <if test="goodsIdx != null and goodsIdx != '' ">
            AND IDX = #{goodsIdx}
        </if>
        <if test="stat != null and stat != '' ">
            AND STAT = #{stat}
        </if>
    </where>
</select>
```

위와 같이 goodsIdx 변수의 데이터형이 Integer 일 때, goodsIdx 값이 0 이라면 goodsIdx != '' 로 인식해서 원하는 조건검색이 안된다. 

<br><br>


# 해결

위에는 샘플이지만, Mybatis를 동적쿼리를 사용 하면 여러 개발자들이 하나의 쿼리를 사용하면서 if 문을 추가하는 경우가 많고 개발하다 보면 임의로 특정 컬럼에 0 을 넣어서 조건검색 하려고 할 때 아래와 같이 `!goodsIdx.equals('')`를 활용하면 된다.

```xml
<if test="goodsIdx != null and !goodsIdx.equals('') ">
    AND IDX = #{goodsIdx}
</if>
```

<br><br>


도움되길 빕니다.

<br>

---