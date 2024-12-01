---
layout: post
title:  "[Practical-Java] 2. Interface default method"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바8에서 새로 생긴 default method는 자바에서 인터페이스 사용시 이미 정해진 인터페이스 규격에서 추가로 변경 될 때 마다 구현클래스에서 매번 메서드 기능을 반드시 구현해야 되는 문제에서 생긴 것이다. 


<br><br>

# 1. 디폴트 메서드(Default Method) 정의

Java 8부터 인터페이스에서 default 키워드를 사용하여 구현할 수 있는 메서드  
인터페이스 내에서 선언과 동시에 기본 동작을 정의할 수 있으며, 이를 통해 인터페이스의 유연성과 하위 호환성을 보장한다.

```java
interface MyInterface {

    void abstractMethod(); // 추상 메서드

    default void defaultMethod() { // 디폴트 메서드
        System.out.println("This is a default method.");
    }
}
```

<br>


## 1-1. Java 8 이전

1. 인터페이스는 순수 추상 클래스 역할로 메서드 명세서만 정의하기에 인터페이스의 모든 메서드는 추상 메서드로만 선언 가능.
2. 기존 인터페이스에 새로운 메서드를 추가하면, 이를 구현하는 모든 기존 클래스가 반드시 추상메서드를 구현해야만 함.
3. 하위 호환성 부족

<br>

## 1-2. Java 8 에서 추가된 주요 디폴트 메서드

1. default 키워드로 메서드를 선언하고, 인터페이스 자체에서 구현 가능함으로 하위 호환성을 보장
2. 기존 인터페이스에 디폴트 메서드를 추가하더라도, 이를 구현한 클래스는 기존 코드를 변경 없이도 동작 가능함으로 코드 중복 감소
3. 터페이스에 공통 동작을 디폴트 메서드로 구현함으로써 코드 중복을 줄이고 유지보수성을 높임
4. 디폴트 메서드는 인터페이스를 단순히 "기능 추가" 목적으로 사용해야 하며, 복잡한 로직을 포함하거나 클래스처럼 사용하지 안항야 함

<br><br>

아래 예제 코드를 보면 초기에 대출실행 인터페이스에서 takeGeneralLoan과 takeCreditLoan 만 정의되어 있다가 나중에 takeCoronaLoan 가 인터페이스에 추가로 정의가 되면 구현클래스에서는 구현하라고 컴파일 에러가 발생한다. 그것을 방지하기 위해 자바8에서부터 인터페이스에 default method 와 변수를 선언 할 수 있다. 그래서 default 메서드를 재정의하지 않아도 된다.


## ◼︎ Class 'ModernBankLoanExecution' must either be declared 에러 발생

```java
interface LoanExecutionInterface {
    //일반 대출
    public boolean takeGeneralLoan(String bankName);
    //신용 대출
    public boolean takeCreditLoan(String bankName);
    //추가 코로나 지원 대출
    public boolean takeCoronaLoan(String bankName);
}

class ModernBankLoanExecution implements LoanExecutionInterface {

    @Override
    public boolean takeGeneralLoan(String bankName) {
        System.out.println(bankName + " 일반 대출 실행");
        return true;
    }

    @Override
    public boolean takeCreditLoan(String bankName) {
        System.out.println(bankName + " 신용 대출 실행");
        return true;
    }

    //에러발생
    //Class 'ModernBankLoanExecution' must either be declared
    //abstract or implement abstract method 'takeCoronaLoan(String)' in 'LoanExecutionInterface'
}
```

<br><br>


## ◼︎ 인터페이스 default method 사용

default 키워드를 통해 컴파일러에게 특별한 메서드라는 것을 알려주기 때문에 default method 를 사용하면 ModernBankLoanExecution 클래스에 takeCoronaLoan를 구현하지 않아도 되며  
여러 구현 클래스에서 공통적으로 사용하는 메서드를 디폴트 메서드에서 구현 할 수 있다.

```java
interface LoanExecutionInterface {
    public static final boolean DefaultBln = true;

    //일반 대출
    public boolean takeGeneralLoan(String bankName);
    //신용 대출
    public boolean takeCreditLoan(String bankName);
    //추가 코로나 지원 대출
    default public boolean takeCoronaLoan(String bankName) {
        if (bankName == null || "".equals(bankName)) {
            System.out.println("코로나 지원 대출 미지원");
        }
        return LoanExecutionInterface.DefaultBln;
    };
}

class ModernBankLoanExecution implements LoanExecutionInterface {

    @Override
    public boolean takeGeneralLoan(String bankName) {
        System.out.println(bankName + " 일반 대출 실행");
        return true;
    }

    @Override
    public boolean takeCreditLoan(String bankName) {
        System.out.println(bankName + " 신용 대출 실행");
        return true;
    }
}

public class MdDefaultMethod {

    public static void main(String[] args) {

        ModernBankLoanExecution modernBankLoanExecution = new ModernBankLoanExecution();
        modernBankLoanExecution.takeGeneralLoan("모던은행");
        modernBankLoanExecution.takeCreditLoan("모던은행");
    }
}
```

<br>

