---
layout: post
title:  "[Practical-Java] 22. Date & Time"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바 7까지의 날짜와 시간을 처리하기 위해 공식적으로 제공하는 클래스는 **Date**와 **Calendar**였으며 날짜와 문자열을 포맷하거나 파싱하기 위해서는 SimpleDateFormat을 주로 사용하였다.

SimpleDateFormat 클래스는 멀티 스레드 환경에서 안전하지 않으며, Date 클래스가 가변성 데이터 구조로 데이터 정합성으로부터 안전하지 않다.

자바 8에서는 날짜와 시간을 `java.util` 패키지가 아닌 `java.time` 패키지로 분리해서 API를 제공하고 있다.

<br>

# 1. 자바 8 `java.time` 패키지

`java.time` 패키지는 자바 8에서 도입된 날짜 및 시간을 다루는 API이다. 이 패키지는 기존의 `java.util.Date`와 같은 날짜-시간 클래스들이 가지고 있던 여러 문제점들을 개선하고, 불변성(Immutable), 명확성, 그리고 스레드 안전성을 제공하도록 설계되었다.

<br>


# 2. 주요 특징

- **불변성**: `java.time`의 모든 주요 클래스는 불변 객체이므로, 한 번 생성된 후에는 그 상태가 변경되지 않는다.
- **체이닝 메서드**: 객체의 상태를 변경하는 메서드들은 항상 새로운 객체를 반환하므로, 메서드 호출을 연쇄적으로 연결할 수 있다.
- **명확성**: 각 클래스는 명확한 목적을 가지고 있으며, 메서드 이름과 기능이 명확하며 일관되게 설계되어 있어 사용하기 쉽다.
- **시간대 지원**: `ZonedDateTime` 등을 사용하여 다양한 시간대를 지원한다.
- **확장성**: 다양한 달력 시스템을 지원하고 사용자 정의 필드 및 단위를 추가할 수 있다.

<br>


# 3. 날짜 클래스

### 3-1. LocalDate 클래스

LocalDate는 날짜만(연, 월, 일) 나타내며 시간 정보는 포함하지 않는다. 이 클래스는 불변(immutable) 객체로 설계되었으며, 주로 날짜를 처리할 때 사용

```java
//1-1. LocalDate
LocalDate today = LocalDate.now(); // 현재 날짜
System.out.println("오늘 날짜: " + today);

LocalDate specificDate = LocalDate.of(2024, 2, 29); // 특정 날짜
System.out.println("특정 날짜: " + specificDate);

LocalDate parsedDate = LocalDate.parse("2024-12-01"); // 문자열 -> 날짜
System.out.println("파싱된 날짜: " + parsedDate);

LocalDate nextDays = today.plusDays(3); //
System.out.println("며칠 이후: " + nextDays);//plusWeeks/minusDays, plusMonths, plusYears

System.out.println("올해 윤년 여부: " + today.isLeapYear());

System.out.println("today 기준 이후 비교 여부: " + today.isAfter(LocalDate.parse("2023-11-01")) );
System.out.println("today 기준 이전 비교 여부: " + today.isBefore(LocalDate.parse("2025-11-01")) );
```
```
오늘 날짜: 2024-12-16
특정 날짜: 2024-02-29
파싱된 날짜: 2024-12-01
며칠 이후: 2024-12-19
올해 윤년 여부: true
today 기준 이후 비교 여부: true
today 기준 이전 비교 여부: true
```

<br>


### 3-2. YearMonth 클래스

YearMonth는 연도와 월을 나타내며, 일(day) 정보는 포함하지 않는다.

```java
YearMonth yearMonth = YearMonth.now(); // 현재 연도와 월
System.out.println("현재 연도와 월: " + yearMonth);

YearMonth specificYearMonth = YearMonth.of(2024, 2); // 특정 연도와 월
System.out.println("특정 연도와 월: " + specificYearMonth);

int daysInMonth = specificYearMonth.lengthOfMonth(); // 해당 월의 일 수
System.out.println("2024년 2월의 일 수: " + daysInMonth);

YearMonth nextMonth = yearMonth.plusMonths(1);
System.out.println("몇 달 후: " + nextMonth);

boolean isValidDay = specificYearMonth.isValidDay(32);
System.out.println("특정 년/월(specificYearMonth)의 해당일이 유효한 날짜인지 체크: " + isValidDay);
```

```
현재 연도와 월: 2024-12
특정 연도와 월: 2024-02
2024년 2월의 일 수: 29
몇 달 후: 2025-01
특정 년/월(specificYearMonth)의 해당일이 유효한 날짜인지 체크: false
```

<br>


### 3-3. MonthDay 클래스

MonthDay는 월과 일만 나타내며 연도 정보는 포함하지 않는다. 주로 기념일, 반복되는 날짜 정보를 처리할 때 유용

```java
MonthDay monthToday = MonthDay.now(); // 현재 월과 일
System.out.println("오늘의 월/일: " + monthToday);

DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM월 dd일");
String formattedDate = monthToday.format(formatter);
System.out.println("오늘의 월/일 포맷: " + formattedDate);

MonthDay specificMonthDay = MonthDay.of(2, 29);
System.out.println("특정 월/일: " + specificMonthDay);
formattedDate = specificMonthDay.format(formatter);
System.out.println("특정 월/일 포맷: " + formattedDate);

boolean isValidYear = specificMonthDay.isValidYear(2024); // 2024년에 유효한 날짜인지 확인
System.out.println("특정 월/일(specificMonthDay)이 해당 년도에 유효한 날짜인지 체크: " + isValidYear);
```

```
오늘의 월/일: --12-16
오늘의 월/일 포맷: 12월 16일
특정 월/일: --02-29
특정 월/일 포맷: 02월 29일
특정 월/일(specificMonthDay)이 해당 년도에 유효한 날짜인지 체크: true
```

<br>


### 3-4. Year 클래스

Year는 연도만 나타내며 주로 연도를 처리하거나 윤년 여부를 확인할 때 사용

```java
Year currentYear = Year.now(); // 현재 연도
System.out.println("현재 연도: " + currentYear);

Year specificYear = Year.of(2028); // 특정 연도
System.out.println("특정 연도: " + specificYear);

boolean isLeap = specificYear.isLeap(); // 윤년 확인
System.out.println("특정년도 윤년 여부: " + isLeap);

Year nextYear = currentYear.plusYears(1); // 다음 해
System.out.println("다음 해: " + nextYear);
```

```
현재 연도: 2024
특정 연도: 2028
특정년도 윤년 여부: true
다음 해: 2025
```

<br>


# 4. 날짜와 시간 클래스

### 4-1. LocalTime 클래스

LocalTime은 시간 정보만(시, 분, 초, 나노초) 포함하며 날짜 정보는 포함하지 않으며, 시간과 관련된 작업에 특화되어 있다.

```java
LocalTime currentTime = LocalTime.now();
System.out.println("현재 시간: " + currentTime);

LocalTime specificTime = LocalTime.of(14, 30, 45);
System.out.println("특정 시간: " + specificTime);

LocalTime parsedTime = LocalTime.parse("12:45:30");
System.out.println("파싱된 시간: " + parsedTime);

LocalTime hoursLater = currentTime.plusHours(1);
System.out.println("몇 시간 후: " + hoursLater);

System.out.println("현재 시간 비교 여부:" + currentTime.isAfter(LocalTime.NOON));

System.out.println("현재시간: " + currentTime.getHour() + "시 " + currentTime.getMinute() + "분 " + currentTime.getSecond() + "초");

formatter = DateTimeFormatter.ofPattern("hh:mm a");
String formatted12 = currentTime.format(formatter);
System.out.println("포맷 시간 (12시간): " + formatted12);

DateTimeFormatter formatter24 = DateTimeFormatter.ofPattern("HH:mm:ss");
System.out.println("포맷 시간 (24시간): " + currentTime.format(formatter24));
```

```
현재 시간: 16:43:52.376319
특정 시간: 14:30:45
파싱된 시간: 12:45:30
몇 시간 후: 17:43:52.376319
현재 시간 비교 여부:true
현재시간: 16시 43분 52초
포맷 시간 (12시간): 04:43 오후
포맷 시간 (24시간): 16:43:52
```

<br>


### 4-2. LocalDateTime 클래스

LocalDateTime은 날짜와 시간 정보를 모두 포함된다. LocalDate와 LocalTime을 결합한 형태로, 날짜-시간 관련 작업에 적합하다.

```java
LocalDateTime currentDateTime = LocalDateTime.now(); // 현재 날짜와 시간
System.out.println("현재 날짜와 시간: " + currentDateTime);

LocalDateTime specificDateTime = LocalDateTime.of(2024, 12, 25, 10, 30);
System.out.println("특정 날짜와 시간: " + specificDateTime);

LocalDateTime parsedDateTime = LocalDateTime.parse("2024-12-25T10:30"); //yyyy-MM-ddTHH:mm:ss
System.out.println("파싱된 날짜와 시간: " + parsedDateTime);

LocalDateTime nextWeek = currentDateTime.plusDays(7);
System.out.println("일주일 후: " + nextWeek);

System.out.println("날짜 부분만: " + currentDateTime.toLocalDate());
System.out.println("시간 부분만: " + currentDateTime.toLocalTime());

formatter = DateTimeFormatter.ofPattern("MM월 dd일, yyyy년 EEEE HH:mm:ss a");
String formattedLocalDateTime = currentDateTime.format(formatter);
System.out.println("포맷 LocalDateTime: " + formattedLocalDateTime);
```

```
현재 날짜와 시간: 2024-12-16T16:57:51.394483
특정 날짜와 시간: 2024-12-25T10:30
파싱된 날짜와 시간: 2024-12-25T10:30
일주일 후: 2024-12-23T16:57:51.394483
날짜 부분만: 2024-12-16
시간 부분만: 16:57:51.394483
포맷된 날짜와 시간 2: 12월 16일, 2024년 월요일 16:57:51 오후
```

<br>


# 5. 타임존과 오프셋 클래스

`타임존`은 번역하면 **시간대**이다. 타임존은 지구의 특정 지역 혹은 국가의 표쥰 시간을 의미한다. `오프셋`은 **시차**를 의미하며 표준시 기준으로 **+-**시차를 가진다.

### 5-1. ZonedDateTime 클래스

ZonedDateTime은 날짜와 시간 정보뿐 아니라 타임존(ZoneId) 정보까지 포함하는 클래스

```java
ZonedDateTime now = ZonedDateTime.now(); // 현재 날짜와 시간 + 타임존
System.out.println("현재 시간: " + now);

ZonedDateTime seoulTime = ZonedDateTime.now(ZoneId.of("Asia/Seoul")); // 특정 타임존
System.out.println("서울 시간: " + seoulTime);

ZonedDateTime newYorkTime = seoulTime.withZoneSameInstant(ZoneId.of("America/New_York")); // 타임존 변환
System.out.println("뉴욕 시간: " + newYorkTime);
```

```
현재 시간: 2024-12-16T17:12:59.127844+09:00[Asia/Seoul]
서울 시간: 2024-12-16T17:12:59.128138+09:00[Asia/Seoul]
뉴욕 시간: 2024-12-16T03:12:59.128138-05:00[America/New_York]
```

<br>

### 5-2. OffsetDateTime 클래스

OffsetDateTime은 날짜와 시간 정보와 함께 **UTC로부터의 시간 차이(Offset)**를 포함한다. 타임존을 정확히 설정할 필요 없이 UTC 오프셋만으로 처리할 때 유용

```java
OffsetDateTime offsetDateTime = OffsetDateTime.now(); // 현재 시간 + UTC 오프셋
System.out.println("현재 시간 (Offset): " + offsetDateTime);

OffsetDateTime specificOffsetDateTime = OffsetDateTime.of(2024, 12, 25, 15, 30, 0, 0, ZoneOffset.ofHours(9)); // KST 오프셋
System.out.println("특정 시간 (KST): " + specificOffsetDateTime);

System.out.println("UTC 시간: " + specificOffsetDateTime.withOffsetSameInstant(ZoneOffset.UTC)); // UTC로 변환
```

```
현재 시간 (Offset): 2024-12-16T17:12:59.130599+09:00
특정 시간 (KST): 2024-12-25T15:30+09:00
UTC 시간: 2024-12-25T06:30Z
```

<br>


### 5-3. OffsetTime 클래스

OffsetTime은 시간과 UTC 오프셋 정보를 포함하며, 날짜는 포함하지 않는다.

```java
OffsetTime offsetTime = OffsetTime.now(); // 현재 시간 + UTC 오프셋
System.out.println("현재 시간 (Offset): " + now);

OffsetTime specificOffsetTime = OffsetTime.of(15, 30, 0, 0, ZoneOffset.ofHours(9)); // KST 오프셋
System.out.println("특정 시간 (KST): " + specificTime);

OffsetTime utcTime = specificOffsetTime.withOffsetSameInstant(ZoneOffset.UTC); // UTC로 변환
System.out.println("UTC 시간: " + utcTime);
```

```
현재 시간 (Offset): 2024-12-16T17:19:19.376434+09:00[Asia/Seoul]
특정 시간 (KST): 14:30:45
UTC 시간: 06:30Z
```

<br>


# 6. 이외 java.time 패키지 클래스

### 6-1. Instant 클래스

Instant는 타임존이나 오프셋 없이 UTC 기준의 시간만을 초와 나노초 단위로 나타냄. 주로 `타임스탬프`를 다룰 때 사용

```java
Instant nowInstant = Instant.now(); // 현재 UTC 시간
System.out.println("현재 UTC 시간: " + nowInstant);

Instant epochSecond = Instant.ofEpochSecond(0); // Epoch 기준 시간
System.out.println("Epoch 기준 시간: " + epochSecond);

Instant oneHourLater = nowInstant.plusSeconds(3600); // 1시간 후
System.out.println("1시간 후 UTC 시간: " + oneHourLater);
```

```
현재 UTC 시간: 2024-12-16T08:21:41.946863Z
Epoch 기준 시간: 1970-01-01T00:00:00Z
1시간 후 UTC 시간: 2024-12-16T09:21:41.946863Z
```

<br>


### 6-2. Period 클래스

Period는 날짜 간의 차이를 연, 월, 일 단위로 나타냅니다.

```java
LocalDate startDate = LocalDate.of(2020, 1, 1);
LocalDate endDate = LocalDate.of(2024, 12, 31);

Period periodBetween = Period.between(startDate, endDate); // 두 날짜 간의 차이 계산
System.out.println("차이: " + periodBetween.getYears() + "년 " + periodBetween.getMonths() + "월 " + periodBetween.getDays() + "일");

Period periodOf = Period.of(1, 6, 15); // 1년 6개월 15일
System.out.println("periodOf: " + periodOf);

// 현재 날짜에 periodOf 더하기
LocalDate periodOfPlus = today.plus(periodOf);
System.out.println("오늘 날짜: " + today);
System.out.println("1년 6개월 15일 후: " + periodOfPlus);
```

```
차이: 4년 11월 30일
periodOf: P1Y6M15D
오늘 날짜: 2024-12-16
1년 6개월 15일 후: 2026-07-01
```

<br>


### 6-3. ChronoUnit 클래스

ChronoUnit은 날짜와 시간 간의 간격을 특정 단위(초, 분, 시간 등)로 계산하거나 비교할 때 사용

```java
LocalDateTime start = LocalDateTime.of(2020, 1, 1, 0, 0);
LocalDateTime end = LocalDateTime.of(2024, 12, 31, 23, 59);

long daysBetween = ChronoUnit.DAYS.between(start, end); // 두 날짜 간의 일수 계산
System.out.println("총 일수 차이: " + daysBetween);

long hoursBetween = ChronoUnit.HOURS.between(start, end); // 두 날짜 간의 시간 계산
System.out.println("총 시간 차이: " + hoursBetween);

LocalDateTime oneYearLater = ChronoUnit.YEARS.addTo(start, 1); // 1년 후
System.out.println("1년 후: " + oneYearLater);
```

```
총 일수 차이: 1826
총 시간 차이: 43847
1년 후: 2021-01-01T00:00
```

<br><br>


---