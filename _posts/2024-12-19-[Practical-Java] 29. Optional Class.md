---
layout: post
title:  "[Practical-Java] 29. Optional Class"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

Java의 `Optional` 클래스는 **null 값을 다루는 데 있어 안전성과 가독성을 높이기 위해 도입된 클래스**이다. `Optional` 클래스는 Java 8에서 추가되었으며,  함수형 프로그래밍을 도입하면서, `Optional` 클래스가 등장해 함수형 스타일로 안전하게 NullPointerException(NPE) 문제를 예방하고 명시적으로 값을 처리하는 방법을 제공한다.

<br>


# 1. Optional 클래스

- `Optional<T>`는 다음 두 가지 상태를 명확히 표현할 수 있는 객체이다.
  - 1) 값이 존재하는 경우: `Optional`이 값을 포함하고 있음
  - 2) 값이 없는 경우: `Optional`이 비어 있음 (`Optional.empty()`)

이러한 상태를 명시적으로 처리함으로써, null 체크 없이도 안전하게 값을 다룰 수 있다.

<br><br>


# 2. Optional의 주요 장점

1. **코드 가독성 향상**  
   - `if (obj != null)` 같은 조건문이 줄어들어 코드가 간결해지고 가독성이 좋아짐

2. **NullPointerException 예방**  
   - `Optional`을 통해 null 검사를 강제하므로, NPE 발생 가능성을 줄임
   - null을 직접 다루지 않는다. 값이 존재하지 않을 때 `null` 대신 비어 있는 `Optional` 객체를 반환

3. **함수형 프로그래밍 활용 가능**  
   - 스트림과 결합하거나, 람다 표현식과 함께 사용해 더 직관적인 코드를 작성

4. **비즈니스 로직에 집중 가능**  
   - null 여부를 확인하는 반복 작업에서 벗어나 본질적인 로직에 집중

<br><br>


# 3. Optional 사용

## 3.1 Optional 객체 생성 방법

Optional 객체를 생성하는 대표적인 세 가지 방법:

#### **1. Optional.of(T value)**

- 값을 반드시 제공해야 하며, `null`을 허용하지 않습니다. `null`을 넣으면 `NullPointerException`이 발생
```java
Optional<String> optional = Optional.of("Hello");
```

#### **2. Optional.ofNullable(T value)**

- 값이 `null`일 수도 있고 아닐 수도 있는 경우 사용합니다. `null`인 경우, 빈 `Optional` 객체가 반환
```java
Optional<String> optional = Optional.ofNullable(null);
```

#### **3. Optional.empty()**

- 값이 없는 빈 `Optional` 객체를 생성
```java
Optional<String> optional = Optional.empty();
```

<br>


## 3.2 Optional 주요 메서드와 예제

#### **1. isPresent() / isEmpty()**

- 값의 존재 여부를 확인
```java
Optional<String> optional = Optional.ofNullable("Hello");

if (optional.isPresent()) {
    System.out.println("값이 존재합니다: " + optional.get());
} else {
    System.out.println("값이 없습니다.");
}
```

#### **2. get()**

- Optional에 값이 있을 경우 해당 값을 반환합니다. 값이 없으면 `NoSuchElementException`이 발생하므로 주의가 필요
```java
Optional<String> optional = Optional.of("Hello");
String value = optional.get(); // "Hello"
```

#### **3. orElse()**

- 값이 존재하면 반환하고, 존재하지 않으면 기본값을 반환
```java
String value = Optional.ofNullable(null).orElse("Default Value");
System.out.println(value); // "Default Value"
```

#### **4. orElseGet(Supplier<? extends T>)**

- 값이 없을 때 동적으로 기본값을 생성하는 함수(Supplier)를 제공
```java
String value = Optional.ofNullable(null).orElseGet(() -> "Generated Value");
System.out.println(value); // "Generated Value"
```

#### **5. orElseThrow(Supplier<? extends Throwable>)**

- 값이 없으면 예외를 throw
```java
Optional<String> optional = Optional.ofNullable(null);
String value = optional.orElseThrow(() -> new IllegalArgumentException("값이 없습니다!"));
```

#### **6. ifPresent(Consumer<? super T>)**

- 값이 존재할 경우, 주어진 작업(Consumer)을 수행
```java
Optional<String> optional = Optional.of("Hello");
optional.ifPresent(value -> System.out.println("값: " + value)); // "값: Hello"
```

#### **7. ifPresentOrElse(Consumer<? super T>, Runnable)**

- 값이 존재하면 첫 번째 작업을 실행하고, 값이 없으면 두 번째 작업을 실행
```java
Optional<String> optional = Optional.ofNullable(null);
optional.ifPresentOrElse(
    value -> System.out.println("값: " + value),
    () -> System.out.println("값이 없습니다.")
);
```

#### **8. map(Function<? super T, ? extends U>)**

- 값이 존재하면 매핑 함수로 변환하여 새 Optional 객체를 반환합
```java
Optional<String> optional = Optional.of("Hello");
Optional<Integer> length = optional.map(String::length);
System.out.println(length.get()); // 5
```

#### **9. flatMap(Function<? super T, Optional< U >> mapper)**

- Optional 내부에서 또 다른 Optional을 생성하는 경우, 중첩을 방지
```java
Optional<String> optional = Optional.of("Hello");
Optional<String> upperCase = optional.flatMap(value -> Optional.of(value.toUpperCase()));
System.out.println(upperCase.get()); // "HELLO"
```

#### **10. filter(Predicate<? super T>)**

- 값이 존재하고 조건(Predicate)을 만족하면 값을 포함한 Optional을 반환하고, 그렇지 않으면 빈 Optional을 반환
```java
Optional<String> optional = Optional.of("Hello");
optional.filter(value -> value.startsWith("H"))
        .ifPresent(value -> System.out.println("조건에 맞는 값: " + value)); // "조건에 맞는 값: Hello"
```

<br>

---

## ◼︎ 사용 예제

`Optional`을 활용한 데이터베이스 조회 처리 예제이다.

```java
import java.util.Optional;

class User {
    private String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

class UserRepository {
    public Optional<User> findById(int id) {
        if (id == 1) {
            return Optional.of(new User("Sui"));
        } else {
            return Optional.empty(); // 사용자 없음
        }
    }
}

public class OptionalExample {
    public static void main(String[] args) {
        UserRepository repository = new UserRepository();
        
        // ID 1로 사용자 검색
        Optional<User> user = repository.findById(1);

        // 값이 존재하면 사용자 이름 출력, 없으면 기본 메시지 출력
        user.ifPresentOrElse(
            u -> System.out.println("사용자 이름: " + u.getName()),
            () -> System.out.println("User NFD")
        );
    }
}
```

<br><br>


---

