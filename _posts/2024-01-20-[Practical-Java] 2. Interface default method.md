---
layout: post
title:  "[Practical-Java] 2. Interface default method"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바8에서 새로 생긴 default method는 자바에서 인터페이스 사용시 이미 정해진 인터페이스 규격에서 추가로 변경 될 때 마다 구현클래스에서 매번 메서드 기능을 반드시 구현해야 되는 문제에서 생긴 것이다. 

아래 예제 코드를 보면 초기에 대출실행 인터페이스에서 takeGeneralLoan과 takeCreditLoan 만 정의되어 있다가 나중에 takeCoronaLoan 가 인터페이스에 추가로 정의가 되면 구현클래스에서는 구현하라고 컴파일 에러가 발생한다. 그것을 방지하기 위해 자바8에서부터 인터페이스에 default method 와 변수를 선언 할 수 있다. 그래서 default 메서드를 재정의하지 않아도 된다.

<br><br>


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

default method 를 사용하면 ModernBankLoanExecution 클래스에 takeCoronaLoan를 구현하지 않아도 된다.

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
