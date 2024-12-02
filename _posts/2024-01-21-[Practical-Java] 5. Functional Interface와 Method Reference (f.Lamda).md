---
layout: post
title:  "[Practical-Java] 5. Functional Interface와 Method Reference (f.Lamda)"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

해당 포스팅은 추후 작성 예정으로 함수형 인터페이스와 메서드 참조에 대한 포스팅이다.

<br><br>



# 1. 함수형 인터페이스(Functional Interface)란?

함수형 인터페이스는 하나의 추상 메서드만 가지는 인터페이스를 의미합니다.
Java에서 함수형 프로그래밍 스타일을 지원하기 위해 사용되며, **람다 표현식(Lambda Expression)**이나 **메서드 참조(Method Reference)**로 해당 인터페이스를 구현할 수 있습니다.

<br>

## 1-1. 함수형 인터페이스의 특징

### 1) 추상 메서드 하나만 포함:  

- 반드시 하나의 추상 메서드를 가져야 하며, 이 추상 메서드는 람다 표현식으로 구현할 수 있다.
- 단, 디폴트 메서드(default)나 정적 메서드(static)는 여러 개 포함할 수 있다.

### 2) @FunctionalInterface 어노테이션:

- 함수형 인터페이스임을 명시적으로 나타내기 위해 사용
- 컴파일 시 함수형 인터페이스 규칙을 강제하여, 두 개 이상의 추상 메서드가 선언되면 컴파일 오류를 발생

### 3) 람다 표현식과의 호환성:
- 함수형 인터페이스를 람다 표현식으로 간단하게 구현할 수 있다.

<br>


## 1-2. 함수형 인터페이스의 장점

### 1) 간결한 코드
- 람다 표현식과 결합하여 코드를 간결하고 명확하게 작성할 수 있습니다.

### 2) 가독성 향상
- 익명 클래스 대신 간단한 람다 표현식을 사용해 가독성을 높일 수 있습니다.

### 3) 유연성
- 다양한 함수형 인터페이스를 활용해 재사용 가능한 코드를 작성할 수 있습니다.

### 4) 병렬 처리 지원
- 스트림 API와 함께 사용하여 병렬 처리를 쉽게 구현할 수 있습니다.

<br>


### 함수형 인터페이스 정의

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b); // 하나의 추상 메서드
}
```

<br>

### 함수형 인터페이스를 익명클래스 방식으로 사용 예제

```java
public class Main {
    public static void main(String[] args) {
    
        Calculator addition = new Calculator() {
            @Override
            public int calculate(int a, int b) {
                return a + b;
            }
        };

        Calculator subtraction = new Calculator() {
            @Override
            public int calculate(int a, int b) {
                return a - b;
            }
        };

        System.out.println(addition.calculate(5, 3)); // 출력: 8
        System.out.println(subtraction.calculate(5, 3)); // 출력: 2
    }
}
```

<br>

### 함수형 인터페이스를 람다식 표현 방식으로 사용 예제

```java
public class Main {
    public static void main(String[] args) {
        // 람다 표현식으로 구현
        Calculator addition = (a, b) -> a + b;
        Calculator subtraction = (a, b) -> a - b;

        System.out.println(addition.calculate(5, 3)); // 출력: 8
        System.out.println(subtraction.calculate(5, 3)); // 출력: 2
    }
}
```

<br>

# 2. Java에서 제공하는 주요 함수형 인터페이스

## 주요 함수형 인터페이스

Java에서 함수형 프로그래밍을 지원하기 위해 `java.util.function` 패키지에서 다양한 함수형 인터페이스를 제공

<br>

| **인터페이스**          | **추상 메서드**                 | **설명**                                                                                      | **예시**                                 |
|------------------------|---------------------------------|---------------------------------------------------------------------------------------------|-----------------------------------------|
| **`Function<T, R>`**   | `R apply(T t)`                 | 입력을 받아 출력으로 변환하는 함수.                                                                  | `(x) -> x * 2`                          |
| **`Consumer<T>`**      | `void accept(T t)`             | 입력을 받아 소비하는 함수. 반환값이 없음.                                                             | `(x) -> System.out.println(x)`          |
| **`Supplier<T>`**      | `T get()`                      | 아무 입력 없이 출력을 제공하는 함수.                                                                 | `() -> new Random().nextInt()`          |
| **`Predicate<T>`**     | `boolean test(T t)`            | 입력을 받아 조건을 테스트하고, 결과를 `boolean`으로 반환.                                                | `(x) -> x > 0`                          |
| **`BiFunction<T, U, R>`** | `R apply(T t, U u)`       | 두 입력 값을 받아 출력으로 변환하는 함수.                                                               | `(x, y) -> x + y`                       |
| **`UnaryOperator<T>`** | `T apply(T t)`                 | 단일 입력을 받아 동일한 타입의 결과를 반환.                                                            | `(x) -> x * x`                          |
| **`BinaryOperator<T>`** | `T apply(T t1, T t2)`         | 두 입력 값을 받아 동일한 타입의 결과를 반환.                                                            | `(x, y) -> x + y`                       |


<br>

## Java 기본 함수형 인터페이스 예제

## 2-1. `Function<T, R>`

입력을 받아 출력을 반환하는 함수.

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        Function<Integer, Integer> multiplyByTwo = x -> x * 2;
        System.out.println(multiplyByTwo.apply(5)); // 출력: 10
    }
}
```

---

## 2-2. `Consumer<T>`

입력을 소비하며 반환값이 없는 함수.

```java
import java.util.function.Consumer;

public class Main {
    public static void main(String[] args) {
        Consumer<String> printMessage = message -> System.out.println("Message: " + message);
        printMessage.accept("Hello, World!"); // 출력: Message: Hello, World!
    }
}
```

---

## 2-3. `Supplier<T>`

입력 없이 출력을 제공하는 함수.

```java
import java.util.function.Supplier;
import java.util.Random;

public class Main {
    public static void main(String[] args) {
        Supplier<Integer> randomNumberSupplier = () -> new Random().nextInt(100);
        System.out.println(randomNumberSupplier.get()); // 출력: (랜덤 숫자, 예: 42)
    }
}
```

---

## 2-4. `Predicate<T>`

조건을 테스트하고 `boolean` 결과를 반환.

```java
import java.util.function.Predicate;

public class Main {
    public static void main(String[] args) {
        Predicate<Integer> isPositive = x -> x > 0;
        System.out.println(isPositive.test(10)); // 출력: true
        System.out.println(isPositive.test(-5)); // 출력: false
    }
}
```

---

## 2-5. `BiFunction<T, U, R>`

두 입력 값을 받아 출력을 반환.

```java
import java.util.function.BiFunction;

public class Main {
    public static void main(String[] args) {
        BiFunction<Integer, Integer, Integer> add = (x, y) -> x + y;
        System.out.println(add.apply(3, 5)); // 출력: 8
    }
}
```

---

## 2-6. `UnaryOperator<T>`

단일 입력을 받아 동일한 타입의 결과를 반환.

```java
import java.util.function.UnaryOperator;

public class Main {
    public static void main(String[] args) {
        UnaryOperator<Integer> square = x -> x * x;
        System.out.println(square.apply(4)); // 출력: 16
    }
}
```

---

## 2-7. `BinaryOperator<T>`

두 입력 값을 받아 동일한 타입의 결과를 반환.

```java
import java.util.function.BinaryOperator;

public class Main {
    public static void main(String[] args) {
        BinaryOperator<Integer> multiply = (x, y) -> x * y;
        System.out.println(multiply.apply(3, 7)); // 출력: 21
    }
}
```

<br>

# 3. 함수형 인터페이스와 메서드 참조

- 함수형 인터페이스는 메서드 참조와도 호환
- 메서드 참조를 통해 기존 메서드나 생성자를 람다 표현식 대신 사용할 수 있다.
- 메서드 참조는 람다 표현식보다 코드가 더 간결해지고, 메서드의 재사용성을 높입니다.
  - 스태틱 메서드 참조: ClassName::methodName
  - 인스턴스 메서드 참조: instance::methodName
  - 생성자 참조: ClassName::new

<br>

아래 예제 코드는 메서드 참조가 아닌 람다 표현식을 사용한 구현이며, 메서드 참조로 작성하려면 기존 메서드(예: add 또는 subtract)를 참조해야 한다.

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}
```

```java
public class Main {
    public static void main(String[] args) {
        // 람다 표현식으로 Calculator 구현
        Calculator addition = (a, b) -> a + b;
        Calculator subtraction = (a, b) -> a - b;

        System.out.println(addition.calculate(5, 3)); // 출력: 8
        System.out.println(subtraction.calculate(5, 3)); // 출력: 2
    }
}
```

위의 람다 표현식은 메서드 참조로 변경될 수 있다.


<br>

## 3-1. 스태틱 메서드 참조

클래스의 스태틱 메서드를 사용하여 메서드 참조로 구현 가능합니다.
예를 들어, 다음과 같이 add와 subtract 메서드를 스태틱으로 정의합니다:

```java
class MathOperations {
    public static int add(int a, int b) {
        return a + b;
    }

    public static int subtract(int a, int b) {
        return a - b;
    }
}
```

### 메서드 참조를 사용하는 코드

```java
public class Main {
    public static void main(String[] args) {
        // 스태틱 메서드 참조로 Calculator 구현
        Calculator addition = MathOperations::add;
        Calculator subtraction = MathOperations::subtract;

        System.out.println(addition.calculate(5, 3)); // 출력: 8
        System.out.println(subtraction.calculate(5, 3)); // 출력: 2
    }
}
```

- MathOperations::add는 (a, b) -> MathOperations.add(a, b)와 동일

<br>

## 3-2. 인스턴스 메서드 참조

인스턴스 메서드를 메서드 참조로 사용할 수도 있다.
예를 들어, 다음과 같이 인스턴스 메서드를 정의

```java
class MathOperations {
    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }
}
```

### 메서드 참조를 사용하는 코드

```java
public class Main {
    public static void main(String[] args) {
        MathOperations operations = new MathOperations(); // 인스턴스 생성

        // 인스턴스 메서드 참조로 Calculator 구현
        Calculator addition = operations::add;
        Calculator subtraction = operations::subtract;

        System.out.println(addition.calculate(5, 3)); // 출력: 8
        System.out.println(subtraction.calculate(5, 3)); // 출력: 2
    }
}
```

- operations::add는 (a, b) -> operations.add(a, b)와 동일

<br>


# 3-3. 생성자 참조

생성자 참조(Constructor Reference)는 반드시 함수형 인터페이스와 함께 사용된다.  
Java에서 생성자 참조는 람다 표현식의 단축 표현이기 떄문이기에 아래 2개 코드는 동일하다.

```java
(args) -> new ClassName(args)
```

```java
ClassName::new
```

<br>

따라서, 인터페이스 없이 생성자 참조를 직접적으로 사용하는 것은 불가능하다. 생성자 참조는 람다 표현식과 마찬가지로, 생성자 참조를 사용하려면 `함수형 인터페이스라는 실행 컨텍스트`가 필요하다.

람다 표현식이나 메서드 참조는 항상 함수형 인터페이스와 결합하여 사용되므로, 다음과 같은 이유로 함수형 인터페이스가 필요하다. 그래서 Java 기본 제공 함수형 인터페이스(Function, Supplier 등)를 사용하면 별도의 사용자 정의 인터페이스 없이도 생성자 참조를 활용할 수 있다.

<br>

### 생성자 참조를 사용하는 코드

Supplier<T>는 입력 없이 객체 T를 반환하는 함수형 인터페이스입니다.
이 인터페이스를 사용하면 기본 생성자를 참조할 수 있습니다.

```java
import java.util.function.Supplier;

class Person {
    private String name;

    public Person() {
        this.name = "Default Name";
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "'}";
    }
}

public class Main {
    public static void main(String[] args) {
        // Supplier 인터페이스로 기본 생성자 참조 사용
        Supplier<Person> personCreator = Person::new;

        // 객체 생성
        Person person = personCreator.get();
        System.out.println(person); // 출력: Person{name='Default Name'}
    }
}
```