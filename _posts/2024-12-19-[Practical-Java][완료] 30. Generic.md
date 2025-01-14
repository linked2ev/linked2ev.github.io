---
layout: post
title:  "[Practical-Java][완료] 30. Generic"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바 제네릭(Generic)은 데이터 타입을 일반화(generalize)하여 코드의 재사용성과 안정성을 높이는 기능이다. 컴파일 시 타입 검사를 강화하고, 타입 변환(casting) 오류를 방지하는 데 유용하다. 제네릭은 클래스, 인터페이스, 메서드에서 사용될 수 있으며, 데이터 타입을 매개변수화(parameterize)하는 방식으로 동작한다.

<br>


# 1. 제네릭

## 1-2. 제네릭의 주요

1. **타입 안정성 제공**
   - 컴파일 시점에 타입 검사를 수행하여 런타임에 발생할 수 있는 타입 오류를 방지
   - 예를 들어, `List<String>`에 문자열만 저장 가능하며, 다른 타입의 데이터 추가를 방지

2. **타입 캐스팅 제거**
   - 명시적 타입 캐스팅 없이 제네릭을 통해 타입을 안전하게 사용
   - 이는 코드의 가독성과 유지보수성을 높이며, 오류를 줄임

3. **코드 재사용성 향상**
   - 타입에 의존하지 않는 제네릭 클래스를 작성하면 다양한 데이터 타입에서 재사용
   - 한 번 작성된 코드가 여러 타입에 대해 동작하므로 중복 코드 작성을 줄임

4. **유연성과 확장성 증가**
   - 와일드카드와 타입 제한(`extends`, `super`)을 통해 제네릭 타입을 유연하게 설정할 수 있음

5. **런타임 성능 최적화**
   - 타입 변환(Casting)이 제거되므로 불필요한 연산이 줄어들어 성능이 향상

<br>


## 1-2. 제네릭 약어와 의미

| **약어** | **의미**                               | **설명**                                                  |
|----------|---------------------------------------|----------------------------------------------------------|
| **E**    | Element                                | 컬렉션의 요소를 나타냄. 예: `List<E>`                     |
| **T**    | Type                                   | 일반적인 데이터 타입. 예: `T`는 어떤 타입이든 가능          |
| **K**    | Key                                    | 맵(Map) 구조에서의 키. 예: `Map<K, V>`에서 키 타입          |
| **V**    | Value                                  | 맵(Map) 구조에서의 값. 예: `Map<K, V>`에서 값 타입          |
| **N**    | Number                                 | 숫자(Number) 타입. 예: `N extends Number`                 |
| **S, U, V** | 여러 타입                           | 두 개 이상의 제네릭 타입 매개변수를 나타냄. 예: `Pair<S, U>` |
| **?**    | Wildcard                               | 특정 타입에 구애받지 않는 임의의 타입. 예: `List<?>`      |

<br><br>


# 2. 제네릭 사용

## (1) 타입 제네릭 (타입 매개변수화)

제네릭은 데이터 타입을 매개변수처럼 정의할 수 있다. 타입 매개변수를 통해 특정 데이터 타입으로 제한하거나, 다양한 타입에 대해 동작하도록 설정할 수 있다. 

```java
class MyAnythingGenericList<E> {
    private List<E> list = new ArrayList<>();

    public void add(E element) {
        list.add(element);
    }

    public  E get(int index) {
        return list.get(index);
    }
}
```

<br>

---

## (2) 멀티타입 제네릭

```java
public class MyAnythingGenericPair<K, V> {
    private K key;
    private V value;

    public MyAnythingGenericPair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public K getKey() {
        return key;
    }

    public V getValue() {
        return value;
    }
}
```

<br>

---

## (3) 제네릭 메서드

```java
public class ExampleGenericMethod {

    public static void main(String[] args) {

        Map<String, Integer> map = new HashMap<>();
        Map<String, Integer> retMap = GenericUtil.<String, Integer>sorting(map);
        //= GenericUtil.sorting(map)
    }
}

class GenericUtil {
    public static <K, V> Map<K, V> sorting(Map<K, V> map) {
        //sorting...
        return map;
    }
}
```

<br>

---

## (4) 제네릭 인터페이스

```java
public interface Container<T> {
    void add(T item);
    T get(int index);
}
```

```java
public class StringContainer implements Container<String> {
    private List<String> list = new ArrayList<>();

    public void add(String item) {
        list.add(item);
    }

    public String get(int index) {
        return list.get(index);
    }
}
```

<br>

---

## (5) 제한 없는 와일드카드(Unbounded Wildcard)

printCollection 메서드는 **Collection<Object>** 로 타입을 제네릭화한 메서드이다. Object 가 최상위 객체이지만 컴파일 시에 String 객체는 컴파일 에러가 발생한다.

즉, **List<Object>**와 **List<String>**는 서로 다른 타입이다. 제네릭에서는 **Object**와 **String**이 서로 호환되지 않는 별개의 타입으로 간주된다는 것이고 Object 가 모든 객체의 부모 클래스이긴 하지만, 제네릭에서는 부모-자식 관계가 적용되지 않는다. 그레서 제네릭에서는 `<?>` 와일드카드를 사용하여 제네릭의 유연성을 확보할 수 있다.

```java
public static void main(String[] args) {

    List<Object> objectList = new ArrayList<Object>();
    List<String> stringList = new ArrayList<String>();

    //컴파일 정상
    printCollection(objectList);
    //컴파일 에러
    printCollection(stringList);
}

private static void printCollection(Collection<Object> c) {
    for (Object e : c) {
        System.out.println(e);
    }
}
```

<br>

`<?>와일드카드`는 제네릭 타입을 좀 더 유연하게 사용할 수 있도록 도와준다.
- 제한 없는 와일드카드(Unbounded Wildcard): `<?>` (모든 타입 허용)
- 상한 경계 와일드카드(Upper-Bounded Wildcard): `<? extends Type>` (특정 타입과 그 하위 타입만 허용)
- 하한 경계 와일드카드(Lower-Bounded Wildcard): `<? super Type>` (특정 타입과 그 상위 타입만 허용)

<br>

위와 같은 메서드는 아래와 같이 `와일드카드`를 사용하거나 제네릭 타입 매개변수 메서드로 대응할 수 있다.

```java
//(1) 와일드카드 사용하기
private static void printWildCardCollection(Collection<?> c) {
    for (Object e : c) {
        System.out.println(e);
    }
}
```

```java
//(2) Object 대신 제네릭 타입 매개변수 사용
private static <T> void printTypeGenericCollection(Collection<T> c) {
    for (T e : c) {
        System.out.println(e);
    }
}
```

<br>

---

## (6) 경계 와일드카드(Bounded Wildcard)

- 상한 경계 와일드카드(Upper-Bounded Wildcard): `<? extends Type>` (특정 타입과 그 하위 타입만 허용)
- 하한 경계 와일드카드(Lower-Bounded Wildcard): `<? super Type>` (특정 타입과 그 상위 타입만 허용)

```java
// 부모 클래스
class Vehicle {
    public void start() {
        System.out.println("Vehicle is starting.");
    }
}

class Car extends Vehicle {
    @Override
    public void start() {
        System.out.println("Car is starting.");
    }
}

class ElectricCar extends Car {
    @Override
    public void start() {
        System.out.println("ElectricCar is starting silently.");
    }
}

// Factory 클래스: Vehicle 및 하위 클래스 관리
class Factory {
    // 상한 경계 와일드카드를 사용하여 Vehicle 및 그 하위 클래스만 허용
    public void serviceVehicles(List<? extends Vehicle> vehicles) {
        for (Vehicle vehicle : vehicles) {
            vehicle.start(); // Vehicle 및 하위 클래스의 start 메서드 호출 가능
        }
    }
}

/**
 * 경계 와일드카드
 */
public class ExampleBoundedGeneric {
    public static void main(String[] args) {
        // 리스트 생성
        List<Vehicle> vehicleList = new ArrayList<>();
        vehicleList.add(new Vehicle());
        vehicleList.add(new Car());
        vehicleList.add(new ElectricCar());

        List<Car> carList = new ArrayList<>();
        carList.add(new Car());
        carList.add(new ElectricCar());

        List<ElectricCar> electricCarList = new ArrayList<>();
        electricCarList.add(new ElectricCar());

        // Factory 인스턴스 생성
        Factory factory = new Factory();

        // Vehicle 및 하위 클래스 리스트 서비스
        System.out.println("# Servicing vehicleList:");
        factory.serviceVehicles(vehicleList);

        System.out.println("# Servicing carList:");
        factory.serviceVehicles(carList);

        System.out.println("# Servicing electricCarList:");
        factory.serviceVehicles(electricCarList);
    }
}
```

<br><br>


# 3. 제네릭의 단점 (제약 조건으로 인한 한계)

(1) **기본 타입(Primitive Type) 사용 불가**  
(2) **런타임 타입 소거(Type Erasure)** (제네릭은 컴파일 시점에만 타입 정보를 유지, 런타임에는 타입 정보가 제거)  
(3) **제네릭 타입의 배열 생성 불가**  
(4) **정적 필드나 메서드에 제네릭 타입 사용 불가**  
(5) **제네릭 타입의 상속 관계와 불변성**  
(6) **타입 캐스팅의 잠재적 오류** (제네릭의 타입 정보가 런타임에 제거되므로, 타입 캐스팅 오류가 발생)  
(7) **복잡성 증가** (코드가 복잡해지고 가독성이 저하)  

<br><br>

