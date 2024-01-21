---
layout: post
title:  "[Practical-Java] 3. Functional Programming"
subtitle:   "[Java]"
categories: java
tags: java-modern-practical
comments: true
---

자바 역사 중에 제일 큰 변화가 온건 함수형 프로그램밍을 도입한 것이다. 아주 예전에 아는 개발자 동생이 함수형 프로그래밍언어 RUST를 추천할 때 처음 가볍게 접했던 기억이 있다. 당시 근무하는 회사의 개발환경에서 사용하지 않았기에 그러려니 했었다. 그만큼 함수형 프로그래밍이 유행이였던 적이 있는데 객체지향 프로그램의 자바 진영에서 자바8때 함수형 프로그램을 도입하며 패러다임 따라갔다고 생각된다.

실제로 자바에서 함수형 프로그램에 의해 stream, 람다식을 이용한 코드로 구현한다. 간혹 이런걸 처음 본 다른 개발자가 그런거 쓰면 안된다고 말은 하지만 그 코드는 그대로 복붙해서 가져다 사용한다. 그만큼 본인이 구현할 코드량보다 코드가 간결하기 때문이다. 

하여튼 이러한 자바의 발전은 자바가 지향하는 사상에 맞게 간다는 느낌이 든다. `하여튼 해당 포스팅에서는 함수형 프로그래밍과 람다식에 대한 내용이다.`

<br><br>


# 1. 함수형 프로그래밍(Functional Programming)

함수형 프로그래밍은 프로그래밍 패러다임 중 하나로 함수형 프로그래밍의 특징은 아래와 같다.

1. **`일급 객체로서의 함수 (First-Class Functions):`** 함수를 일급 객체로 취급합니다. 함수를 변수에 할당하고, 함수의 매개변수로 전달하며, 함수를 반환할 수 있습니다.

2. **`불변성 (Immutability):`** 데이터의 불변성을 강조합니다. 한 번 생성된 데이터는 변경할 수 없으며, 변경이 필요한 경우에는 새로운 데이터를 생성하여 사용합니다.

3. **`순수 함수 (Pure Functions):`** 함수는 외부의 상태를 변경하지 않고, 같은 입력에 대해서 항상 같은 출력을 반환하는 특성을 갖습니다. 부작용(side effects)을 최소화하여 코드의 안정성을 높이고 테스트가 쉽게 가능하게 합니다.

4. **`고계 함수 (Higher-Order Functions):`** 함수를 매개변수로 받거나 함수를 반환할 수 있습니다. 이는 함수를 값처럼 다룰 수 있어 코드를 간결하게 만들고, 추상화 수준을 높일 수 있습니다.

5. **`데이터 표현과 변환:`** 데이터를 변경하는 대신 변환하는 방식에 중점을 둡니다. 이는 데이터를 불변하게 다루고, 변환 함수를 통해 데이터를 조작하는 방식을 촉진합니다.

6. **`레퍼런스 투명성 (Referential Transparency):`** 동일한 입력에 대해 항상 동일한 결과를 반환하는 특성을 갖습니다. 이는 코드를 이해하기 쉽게 만들어주고, 코드의 예측 가능성을 높입니다.

7. **`재귀(Recursion):`** 반복문 대신 재귀를 사용하여 반복을 처리합니다. 재귀는 함수형 프로그래밍에서 자주 사용되며, 많은 함수형 언어에서 재귀를 지원합니다.

8. **`합성(Composition):`** 여러 함수를 조합하여 새로운 함수를 만드는 것을 장려합니다. 이로써 모듈화와 재사용성이 높아지며, 코드의 가독성도 향상됩니다.

이러한 특징들은 함수형 프로그래밍이 높은 수준의 `추상화와 모듈화`를 통해 코드의 가독성을 높이고, `병렬 프로그래밍 및 상태 관리`에 있어서도 유리하다는 장점이 있다. 이건 여러 기능들을 개발하다 보면 무의식적으로 위에 특징이 느껴진다. 

<br><br>


## ◼︎ 함수형 프로그래밍 예제

```java
@FunctionalInterface
interface ConditionLoanProduct {
    boolean filterLoanProduct(LoanProduct loanProduct);
}

public class MdFunctionalProgramming {

    public static void main(String[] args) {

        List<LoanProduct> loanProductList = new ArrayList<>();
        //더미 데이터 생성
        loanProductList = makeDummyData();

        //함수형 인터페이스와 다형성을 이용한 10,000,000원 이하 대출 상품과 30,000,000원 이상 대출 상품을 조회하는 함수형 프로그램밍이다.
        //1. 대출 최대금액이 10,000,000원 이하 대출 상품을 조회하는 인터페이스를 구현
        ConditionLoanProduct filterByLessThen10000000 = new ConditionLoanProduct() {
            @Override
            public boolean filterLoanProduct(LoanProduct loanProduct) {
                if (loanProduct.getMaxLoanAmt().compareTo(new BigDecimal("10000000")) < 0) {
                    return true;
                }
                return false;
            };
        };

        //2. 대출 최대금액이 30,000,000원 이상 대출 상품을 조회하는 인터페이스를 구현
        ConditionLoanProduct filterByMoreThen30000000 = new ConditionLoanProduct() {
            @Override
            public boolean filterLoanProduct(LoanProduct loanProduct) {
                if (loanProduct.getMaxLoanAmt().compareTo(new BigDecimal("30000000")) > 0) {
                    return true;
                }
                return false;
            };
        };

        //10,000,000원 이하 대출 상품
        List<LoanProduct> loanProductListByLessThen10000000 = getLoanProductListByCondition(filterByLessThen10000000, loanProductList);

        //30,000,000원 이상 대출 상품
        List<LoanProduct> loanProductListByMoreThen30000000 = getLoanProductListByCondition(filterByMoreThen30000000, loanProductList);


        System.out.println("----- 10,000,000원 이하 대출 상품 -----");
        for (LoanProduct p : loanProductListByLessThen10000000) {
            System.out.println(p);
        }

        System.out.println("----- 30,000,000원 이상 대출 상품 -----");
        for (LoanProduct p : loanProductListByMoreThen30000000) {
            System.out.println(p);
        }
    }

    /**
     * 함수형 인터페이스를 이용한 조건에 맞는 대출상품 조회 메서드
     * @param conditionLoanProduct
     * @param loanProductList
     * @return
     */
    public static List<LoanProduct> getLoanProductListByCondition(ConditionLoanProduct conditionLoanProduct, List<LoanProduct> loanProductList) {
        List<LoanProduct> ret = new ArrayList<>();

        for (LoanProduct loanProduct : loanProductList) {
            if (conditionLoanProduct.filterLoanProduct(loanProduct)) {
                ret.add(loanProduct);
            }
        }
        return ret;
    };

    /**
     * 더미 데이터 생성
     * @return
     */
    public static List<LoanProduct> makeDummyData() {
        List<LoanProduct> list = new ArrayList<>();
        LoanProduct p = new LoanProduct();

        for (int i=0; i<10; i++) {
            p = new LoanProduct();
            p.setId(i + "");
            p.setName("대출상품_" + i);
            p.setMaxLoanAmt(getRandomBigDecimalBetweenRange());
            list.add(p);
        }
        return list;
    };

    /**
     * 지정된 범위에서 랜덤한 BigDecimal 값을 반환하는 메서드
     * @return
     */
    private static BigDecimal getRandomBigDecimalBetweenRange() {
        int minValue = 3;
        int maxValue = 100;

        // 랜덤 객체 생성
        Random random = new Random();
        // 범위 크기 계산
        int range = maxValue - minValue + 1;
        // 0부터 1 사이의 랜덤한 double 값 생성
        int randomInt = random.nextInt(range);
        // 범위에 비례하는 랜덤한 BigDecimal 값 생성
        int randomBigDecimal = randomInt * 1000000;
        return BigDecimal.valueOf(randomBigDecimal);
    }
}

class LoanProduct {
    private String id;
    private String name;
    private BigDecimal maxLoanAmt;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public BigDecimal getMaxLoanAmt() {
        return maxLoanAmt;
    }

    public void setMaxLoanAmt(BigDecimal maxLoanAmt) {
        this.maxLoanAmt = maxLoanAmt;
    }

    @Override
    public String toString() {
        return "LoanProduct{" +
                "id='" + id + '\'' +
                ", name='" + name + '\'' +
                ", maxLoanAmt=" + maxLoanAmt +
                '}';
    }
}
```

```
----- 10,000,000원 이하 대출 상품 -----
LoanProduct{id='6', name='대출상품_6', maxLoanAmt=1000000}
LoanProduct{id='9', name='대출상품_9', maxLoanAmt=6000000}
----- 30,000,000원 이상 대출 상품 -----
LoanProduct{id='0', name='대출상품_0', maxLoanAmt=64000000}
LoanProduct{id='1', name='대출상품_1', maxLoanAmt=72000000}
LoanProduct{id='2', name='대출상품_2', maxLoanAmt=94000000}
LoanProduct{id='4', name='대출상품_4', maxLoanAmt=56000000}
LoanProduct{id='5', name='대출상품_5', maxLoanAmt=94000000}
LoanProduct{id='7', name='대출상품_7', maxLoanAmt=67000000}
LoanProduct{id='8', name='대출상품_8', maxLoanAmt=42000000}
```

<br><br>


# 2. 람다식(Lambda Expression)

람다식이란 함수(메서드)를 간단한 식(expression)으로 표현하는 방법을 의미한다. 함수형언어에서부터 나온 것이다. 람다식은 자바 8부터 도입된 함수형 프로그래밍의 핵심 요소 중 하나이다. 람다식의 주요 특징은 아래와 같다.

1. **`간결성:`** 람다식은 매우 간결하고 직관적인 문법을 가지고 있어 코드의 길이를 크게 줄여줍니다. 특히, 간단한 작업을 수행하는 함수를 간결하게 표현할 수 있습니다.

2. **`익명 함수:`** 이름이 없는 함수로, 메서드와 같이 독립적으로 사용할 수 있는 코드 블록입니다. 메서드처럼 호출할 필요 없이 바로 사용할 수 있습니다.

3. **`함수형 프로그래밍 지원:`** 람다식은 함수형 프로그래밍의 핵심적인 요소로, 함수를 일급 객체로 취급하여 변수에 할당하고 전달할 수 있습니다.

4. **`함수형 인터페이스 지원:`** 람다식은 함수형 인터페이스에 사용됩니다. 함수형 인터페이스는 딱 하나의 추상 메서드를 가진 인터페이스이며, 람다식은 이를 구현하여 사용됩니다.

5. **`스트림 API와의 호환성:`** 람다식은 자바의 스트림 API와 함께 사용되어 데이터를 처리하고 조작하는 데 매우 효과적입니다. 스트림 API에서 람다식을 이용하여 데이터를 다루는 것이 가능합니다.

6. **`병렬 프로그래밍 지원:`** 병렬 처리를 위해 람다식이 적합합니다. 간단하게 병렬 실행을 할 수 있고, 병렬 프로그래밍을 위한 기능을 제공합니다.

7. **`클로저:`** 람다식은 자신을 포함하는 범위에 있는 변수를 캡처할 수 있습니다. 이러한 능력을 클로저라고 하며, 외부 범위의 변수를 람다식 내부에서 사용할 수 있게 합니다.

<br><br>

## ◼︎ 람다식 예제

함수형 프로그램밍으로 구현 된 코드를 람다식 표현을 변경한 코드이다.

> 함수형 프로그램밍

```java
//함수형 프로그래밍와 다형성을 이용한 10,000,000원 이하 대출 상품과 30,000,000원 이상 대출 상품을 조회하는 함수형 프로그램밍이다.
//1. 대출 최대금액이 10,000,000원 이하 대출 상품을 조회하는 인터페이스를 구현
ConditionLoanProduct filterByLessThen10000000 = new ConditionLoanProduct() {
    @Override
    public boolean filterLoanProduct(LoanProduct loanProduct) {
        if (loanProduct.getMaxLoanAmt().compareTo(new BigDecimal("10000000")) < 0) {
            return true;
        }
        return false;
    };
};

//2. 대출 최대금액이 30,000,000원 이상 대출 상품을 조회하는 인터페이스를 구현
ConditionLoanProduct filterByMoreThen30000000 = new ConditionLoanProduct() {
    @Override
    public boolean filterLoanProduct(LoanProduct loanProduct) {
        if (loanProduct.getMaxLoanAmt().compareTo(new BigDecimal("30000000")) > 0) {
            return true;
        }
        return false;
    };
};
```

<br>

> 람다식

확실히 함수형 프로그래밍에서 람다식으로 구현시에 코드의 간결함을 볼 수 있다.

```java
//함수형 인터페이스와 다형성을 이용한 10,000,000원 이하 대출 상품과 30,000,000원 이상 대출 상품을 조회하는 함수형 프로그램밍이다.
//1. 대출 최대금액이 10,000,000원 이하 대출 상품을 조회하는 인터페이스를 구현 (람다식 표현)
ConditionLoanProduct filterByLessThen10000000 = loanProduct -> loanProduct.getMaxLoanAmt().compareTo(new BigDecimal("10000000")) < 0;

//2. 대출 최대금액이 30,000,000원 이상 대출 상품을 조회하는 인터페이스를 구현 (람다식 표현)
ConditionLoanProduct filterByMoreThan30000000 = loanProduct -> loanProduct.getMaxLoanAmt().compareTo(new BigDecimal("30000000")) > 0;
```

<br><br>
