---
layout: post
title:  "[MyBatis] foreach 구문, parameterType List, Model, Map 사용"
subtitle:   "[Mybatis]"
categories: mybatis
tags: mybatis-posting
comments: true
---

MyBatis로 foreach 구문, parameterType List Model Map 사용

<br>

# 이슈

Mybatis foreach 사용법이 생각이 안나서 정리함.

<br>

# 해결

1. Mapper 파라미터가 List<Model객체> 일 경우
2. Mapper 파라미터가 Model객체 내 List를 foreach로 활용 할 경우
3. Mapper 파라미터가 Map 일 경우

<br><br>


## 1. Mapper 파라미터가 List<Model객체> 일 경우 
---

> @Mapper

```java
void insertBoardList(List<Board> boardList) throws Exception;
```

> SQL Mapper

```sql
<insert id="insertBoard" parameterType="java.util.List">
    INSERT INTO T_BOARD
    (
        TITLE
        , CONTENTS
        , DISPLAY_YN
        , EMAIL
        , REG_ID
        , REG_DATE
    )
    VALUES
    (
    <foreach collection="list" item="item" separator=",">
        #{item.title}
        , #{item.contents}
        , #{item.displayYn}
        , #{item.email}
        , #{item.regId}
        , NOW()
    </foreach>
    )
</insert>
```

`! collection="list" item="item" 는 defualt 이다.`

<br><br><br>



## 2. Mapper 파라미터가 Model객체 내 List를 foreach로 활용 할 경우
---

> Board.java (참고용)

```java
@Data
public class Board implements Serializable  {

    public Integer boardIdx;
    //...생략...
    List<BoardReply> boardReplyList;
}
```

> @Mapper

```java
BoardReply selectBoardReply(Board board) throws Exception;
```

```sql
<select id="selectBoardReply" parameterType="BoardSearch" resultType="BoardReply">
    SELECT
        BOARD_IDX
        , REPLY_IDX
        , TITLE
        , CONTENTS
    FROM
        T_BOARD_REPLY
    WHERE
        BOARD_IDX = #{boardIdx}
        AND REPLY_IDX NOT IN (
        <foreach collection="boardReplyList" item="item" separator=",">
            #{item.replyIdx}
        </foreach> )
</select>
```

`! collection Id 값과 parameterType 객체 내 필드명을 동일하게 해야함`

<br><br><br>



## 3. Mapper 파라미터가 Map 일 경우
---

> ServiceImpl.java (참고용

```java
@Override
public void insertEventGoodsMng(Map<String, Object> dbParams) {
    // dbParams={eventIdx=113, cate=24, ...}
    List<Map<String, Object>> goodsList = boardMapper.selectGoodsList(dbParams);
    dbParams.put("goodsList", goodsList);
    eventMapper.insertEventGoodsMng(dbParams);
}
```

```sql
<insert id="insertEventGoodsMng" parameterType="java.util.HashMap">
    <if test="goodsList.size != 0">
        INSERT INTO EVENT_GOODS_MNG
        (
            EVENT_GOODS_MNG_IDX
            , GOODS_IDX
        )
        VALUES
        <foreach collection="goodsList" item="item" separator=",">
        (
            #{eventIdx}
            , #{item.goodsIdx}
        )
        </foreach>
    </if>
</insert>
```

<br><br>

도움되길 빕니다.

<br>

---