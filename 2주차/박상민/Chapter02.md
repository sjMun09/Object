# 📕 [Object] 2장. 객체지향 프로그래밍

# ⭐️ 객체지향 프로그래밍을 향해
## 📌 협력, 객체, 클래스
진정한 객체지향 패러다임으로의 전환은 클래스가 아닌 객체에 초점을 맞출 때에만 얻을 수 있다. </br>
이를 위해서는 프로그래밍하는 동안 다음의 두 가지에 집중해야 한다.

- **어떤 클래스가 필요한지를 고민하기 전에 어떤 객체들이 필요한지 고민하라.**
    - 클래스는 공통적인 상태와 행동을 공유하는 객체들을 추상화한 것이다. 따라서 클래스의 윤곽을 잡기 위해서는어떤 객체들이 어떤 상태와 행동을 가지는지 먼저 결정해야 한다.</br> 객체를 중심에 두는 접근 방법은 실체를 단순하고 깔끔하게 만든다.

- **객체를 독립적인 존재가 아니라, 기능 구현을 위해 협력하는 공동체의 일원으로 봐야 한다.
    - 객체는 홀로 존재하는 것이 아니다. 다른 객체에게 도움을 주거나 의존하면서 살아가는 협력적인 존재이다. 객체를 협력하는 공동체의 일원으로 바라보는 것은 설계를 유연하고 확장 가능하게 만든다.

## 📌 도메인의 구조를 따르는 프로그램 구조
**도메인(domain)**
- 문제를 해결하기 위해 사용자가 프로그램을 사용하는 분야

**객체지향 패러다임이 강력한 이유**
- 요구사항을 분석하는 초기 단계부터, 프로그램을 구현하는 마지막 단계까지 객체라는 동일한 추상화 기법을 사용할 수 있기 때문이다.
  요구사항과 프로그램을 갹체라는 동일한 관점에서 보기 때문에 도메인을 구성하는 개념들이 프로그램의 객체와 클래스로 매끄럽게 연결된다.

![](https://velog.velcdn.com/images/pp8817/post/1aa84d37-d6a8-41d1-abf3-b58e0b4713b5/image.png)
일반적으로 클래스 기반의 객체지향 언어에서는 도메인 개념을 구현하기 위해 Class를 사용한다.</br>
Class의 이름은 대응되는 도메인 개념의 이름과 동일하게 하거나 적어도 유사해야 한다.</br>
Class 사이의 관계도 최대한 도메인 개념 사이에 맺어진 관계와 유사하게 만들어야 프로그램의 구조를 이해하고, 예상하기 쉬워진다.

따라서 Class 다이어그램은 도메인 모델과 유사한 형태를 보이게 된다. 다음 사진과 같이 말이다.
![](https://velog.velcdn.com/images/pp8817/post/c5353a02-1628-4266-87c0-b54d934d7eb5/image.png)

## 📌 자율적이 객체
**객체의 2가지 중요한 사실**
- 객체는 상태(state)와 행동(behavior)을 함께 가지는 복합적인 존재이다.
- 객체는 스스로 판단하고, 행동하는 자율적인 존재이다.

객체지향 패러다임에서는 객체라는 단위 안에 **데이터와 기능을 한 덩어리로 묶음으로써 _캡슐화_ 시킬 수 있다.**  </br>객체지향 프로그래밍 언어들은 상태와 행동을 캡슐화하는 것에서 한 걸음 더 나아가 외부에서의 접근을 통제할 수 있는 **접근 제어(access control)** 메커니즘도 함께 제공한다.
객체지향 언어에서는 public, protected, private 와 같은 **접근 수정자**를 제공한다.

**왜 객체 내부에 대한 접근을 통제할까?** </br>
바로 객체를 자율적인 존재로 만들기 위해서다. 객체지향의 핵심은 스스로 상태를 관리하고, 판단하고, 행동하는 자율적인 객체들의 공동체를 구성하는 것이다. 객체가 자율적인 존재로 서기 위해서는 외부의 간섭을 최소화해야 한다. 그렇기 때문에 객체 내부에 대한 접근을 통제한다.

외부에서 객체의 결정에 직접적으로 개입해서는 안된다. 객체에게 요청만 하고, 스스로 최선의 방법을 경청하도록 해야한다.
- - -
캡슐화와 접근 제어는 객체를 두 부분으로 나눈다.
- 외부에서 접근 가능한 부분: **public interface**
- 외부에서는 접근 불가능하고, 오직 내부에서만 접근 가능한 부분: **implementatin(구현)**

일반적으로 Java의 경우 클래스의 인스턴스 변수의 가시성은 private이고, 외부에 제공해야 할 메서드는 public이다.
public interface에는 public으로 지정된 메서드만 포함된다.
그 외의 private 메서드, 인스턴스 변수는 implementation에 해당한다.

## 📌 협력하는 객체들의 공동체

Screening(상영), Money(금액), Movie(영화), Reservation(예약) 클래스를 보여줄 것이다. 이때 코드를 볼 때 영화 예매라는 기능을 구현하기 위한 객체들 사이의 이뤄지는 상호작용을 유의해서 지켜보자.

**Screening(상영)**
```java
public class Screening {
    private Movie movie;

    public Reservation reserve(Customer customer, int audienceCount) {
        return new Reservation(customer, this, calculateFee(audienceCount),
                audienceCount);
    }

    private Money calculateFee(int audienceCount) {
        return movie.calculateMovieFee(this).times(audienceCount);
    }
}
```

**Money(금액)**
```java
public class Money {
    public static final Money ZERO = Money.wons(0);

    private final BigDecimal amount;

    public static Money wons(long amount) {
        return new Money(BigDecimal.valueOf(amount));
    }

    public static Money wons(double amount) {
        return new Money(BigDecimal.valueOf(amount));
    }

    Money(BigDecimal amount) {
        this.amount = amount;
    }

    public Money plus(Money amount) {
        return new Money(this.amount.add(amount.amount));
    }

    public Money minus(Money amount) {
        return new Money(this.amount.subtract(amount.amount));
    }

    public Money times(double percent) {
        return new Money(this.amount.multiply(BigDecimal.valueOf(percent)));
    }

    public boolean isLessThan(Money other) {
        return amount.compareTo(other.amount) < 0;
    }

    public boolean isGreaterThanOrEqual(Money other) {
        return amount.compareTo(other.amount) >= 0;
    }
}
```

**Reservation(예약)**
```java
public class Reservation {
    private Customer customer;
    private Screening Screening;
    private Money fee;
    private int audienceCount;

    public Reservation(Customer customer, Screening Screening, Money fee, int audienceCount) {
        this.customer = customer;
        this.Screening = Screening;
        this.fee = fee;
        this.audienceCount = audienceCount;
    }
}
```
영화 예매라는 기능을 구현하기 위한 객체들 사이의 이뤄지는 상호작용을 보았는가?
이것을 **협력(Collaboration)** 이라고 부른다.

객체지향 프로그램을 작성할 때는 먼저 협력의 관점에서 어떤 객체가 필요한지를 결정하고, 객체들의 공통 상태와 행위를 구현하기 위해 클래스를 작성한다.

### ✔︎ 협력에 관한 짧은 이야기
객체는 다른 객체의 인터페이스에 공개된 행동을 수행하도록 **요청(request)** 할 수 있다. 요청을 받은 객체는 **자율적인** 방법에 따라 요청을 처리한 후 **응답(response)**한다.

객체가 다른 객체와 상호작용하는 유일한 방법은 메시지를 전송(send a message)하는 것 뿐이다. </br>
메시지를 수신한 객체는 스스로의 결정에 따라 자율적으로 메시지를 처리할 방법을 결정한다.

이처럼 수신된 메시지를 처리하기 위한 자신만의 방법을 **메서드(method)**라고 부른다.

위에서 소개했던 코드의 일부분을 다시 보자.

```java
public class Screening {
    private Money calculateFee(int audienceCount) {
        return movie.calculateMovieFee(this).times(audienceCount);
    }
}
```
이 코드의 calculateFee 메서드를 설명할 때 어떻게 말할 것인가?

1. Screening이 Movie의 calculateMovieFee '메서드를 호출한다'
2. Screening이 Movie에게 calculateMovieFee '메시지를 전송한다'

정답은 2번이다.
사실 Screening은 Movie 안에 calculateMovieFee 메서드가 존재하고 있는지 알 수 없다.
단지, Movie가 calculateMovieFee 메시지에 응답할 수 있다고 믿고 메시지를 전송할 뿐이다.

메시지를 받은 Movie는 스스로 적절한 메서드를 선택한다.

# ⭐️ 할인 요금 구하기
## 📌 할인 요금 계산을 위한 협력 시작하기
**Movie**
```java
public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
    this.title = title;
    this.runningTime = runningTime;
    this.fee = fee;
    this.discountPolicy = discountPolicy;
}
```
Movie의 생성자를 잘 살펴보자.

분명 할인 전략을 받고 있긴 하지만, 어떠한 할인 전략을 사용할지 명시가 되어있지 않다. </br>
이 코드에는 객체지향에서 중요한 개념 두가지가 숨겨져있는데,
하나는 **상속**, 하나는 **다형성**이다. 그 기반에는 **추상화**의 원리가 숨겨져 있다.

**DiscountPolicy**
```java
public abstract class DiscountPolicy {
    private List<DiscountCondition> conditions = new ArrayList<>();

    public DiscountPolicy(DiscountCondition ... conditions) {
        this.conditions = Arrays.asList(conditions);
    }

    public Money calculateDisCountAmount(Screening screening) {
        for (DiscountCondition each : conditions) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }

        return Money.ZERO;
    }

    abstract protected Money getDiscountAmount(Screening screening);
}
```
상위 클래스인 DiscountPolicy 안에 중복 코드를 두고, AmountPolicy, PercentDiscountPolicy가 이 클래스를 상속한다. </br>
실제 애플리케이션에서 DiscountPolicy의 인스턴스를 생성할 필요가 없기 때문에 abstract class(추상 클래스)로 구현했다.

DiscountPolicy는 할인 여부와 요금 계산에 필요한 전체적인 흐름을 정의하지만, 실질적인 요금을 계산하는 부분을 추상메서드인 getDiscountAmount() 메서드에 위임한다.

이처럼 기본적인 알고리즘의 흐름을 결정하고, 중간에 필요한 처리를 하위 클래스에게 위임하는 디자인 패턴을 `TEMPLATE METHOD 패턴` 이라고 부른다.
> 외부 라이브러리들이 이 패턴을 많이 사용한다.

# ⭐️ 상속과 다형성
**Movie**
```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        this.title = title;
        this.runningTime = runningTime;
        this.fee = fee;
        this.discountPolicy = discountPolicy;
    }

    public Money getFee() {
        return fee;
    }

    public Money calculateMovieFee(Screening screening) {
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```
Movie클래스 어디에도 할인 정책이 정액제인지 정률제인지 정의되어 있지 않다.
그런데, 어떻게 요금 계산시 할인 정책을 정할 수 있을까?

이를 알기 위해서는 상속과 다형성을 알아야한다.

## 📌 컴파일 시간 의존성과 실행 시간 의존성
![](https://velog.velcdn.com/images/pp8817/post/e5827ca6-c08f-471f-a1ac-77c54a70d748/image.png)
위의 그림은 Movie와 DiscountPolicy 계층 사이의 관계를 클래스 다이어그램으로 표현한 것이다.

Movie는 DiscountPolicy와 연결되어 있으며, AmountDiscountPolicy와 PercentDiscountPolicy는 추상 클래스인 DiscountPolicy를 상속받는다.

여기서 자세히 봐야 할 부분은 Movie 클래스가 AmountDiscountPolicy, PercentDiscountPolicy와 같은 인스턴스에 의존하는 것이 아닌 **추상 클래스에 의존**하고 있다는 것이다.
- - -
그렇다면 Movie의 인스턴스가 코드 작성 시점에는 존재조차 모르던 AmountDiscountPolicy, PercentDiscountPolicy의 인스턴스와 실행 시점에 협력 가능한 이유는 무엇일까?

Movie의 인스턴스를 생성하는 코드를 살펴보자

```java
Movie avartar = new Movie("아바타",
	Duration.ofMinutes(120),
    Money.wons(10000),
    new PercentDisCountPolicy(0,1,....));
```
코드상에서 Movie는 DiscountPolicy 인스턴스를 의존한다. 그러나 생성시점에 PercentDiscountPolicy 인스턴스를 전달하면 해당 인스턴스로 생성할 수 있다.

![](https://velog.velcdn.com/images/pp8817/post/5b46cb46-faff-475a-b018-0cf8039ddf27/image.png)
코드 상에서 Movie는 DiscountPolicy에 의존한다. 그러나 실행 시점에는 Movie의 인스턴스는 DiscountPolicy를 상속한 하위 클래스의 인스턴스에 의존한다.

**핵심은 코드의 의존성과 실행 시점의 의존성이 서로 다를 수 있다는 점이다.**

>**간과하지말자**
유연한게 아무리 좋다지만, 실행, 컴파일 시점의 의존성이 다를 수록 코드는 유연해진다. </br>
하지만, 코드를 이해하기 어려워진다. 현재 Movie 인스턴스가 어떤 의존성을 가지고 있는지 실행시점마다 변경될 수 있기때문에
디버깅하기가 어려워질 수 있다. 이처럼 무조건 유연한 설계도, 읽기 쉬운 코드도 정답이 아니다.

## 📌 상속과 인터페이스

상속이 가치 있는 이유는 **부모 클래스가 제공하는 모든 인터페이스를 자식 클래스가 물려받을 수 있기 때문**이다.
(단순히 메서드나 인스턴스 변수를 재사용하는 것이 아닌)
>상속은 그저 부모 클래스의 것을 물려받는 것이 아니다.
상속의 핵심은 **확장(extends)**에 있다.

Movie의 calculateMovieFee 메서드와 같이 discountPolicy.calculateDiscountamount 메서드를 호출 할 때, DiscountPolicy를 상속받은 인스턴스들이 이 인터페이스를 알고 있기 때문에, 해당 인스턴스들이 들어와도 메시지를 송수신할 수 있는 것이다.

컴파일러는 코드상에서 부모 클래스가 나오는 모든 장소에서 자식 클래스를 사용하는 것을 허용한다.

이처럼 자식 클래스가 부모클래스를 대신 하는 것을 **업캐스팅**이라고 부른다.

Movie 입장에서는 자신과 협력하는 객체가 어떤 클래스의 인스턴스인지가 중요한 것이 아니라, calculateDiscountAmount라는 메시지를 수신할 수 있다는 사실이 중요하다.

## 📌 다형성
**메시지**와 **메서드**의 차이를 알자.

Movie는 DiscountPolicy의 인스턴스에게 calculateDiscountAmount 메시지를 전송한다. </br>
이후 연결된 인스턴스가 **오버라이딩된 메서드를 실행**한다.
Movie가 동일한 메시지를 전송하지만, 실제로 어떤 메서드가 실행될 것인지는 메시지를 수신하는 객체 클래스가 무엇이냐에 따라 달라지는데, 이를 **다형성**이라고 부른다.

그리고 메시지와 메서드를 실행 시점에 바인딩 하는 것을 지연 바인딩(lazy binding), 동적 바인딩(dynamic binding)이라고 부른다.

> 그에 반해 전통적인 함수 호출처럼 컴파일 시점에 실행될 함수나 프로시져를 결정하는 것을 초기 바인딩, 적정바인딩이라고 부른다.

하나의 메시지를 선택적으로 서로 다른 메서드에 연결 할 수 있는 것은 지연바인딩이라는 메커니즘을 사용하기 때문이다.

# ⭐️ 추상화와 유연성
## 📌 추상화의 힘
![](https://velog.velcdn.com/images/pp8817/post/62afd809-93ee-42cc-a8f9-59924b3cfb2c/image.png)
위 그림은 추상화를 사용할 경우 2가지 장점을 보여준다.

1. 추상화의 계층만 따로 떼어놓고 보면 **요구 사항의 정책을 높은 수준에서 서술**할 수 있다.
- ex) 영화 예매 요금은 최대 하나의 할인 정책과 다수의 할인 조건을 이용해 계산할 수 있다.
2. 추상화를 이용하면 설계갸 유연해진다.

위 다이어그램을 하나의 문장으로 정리하면
"영화 예매 요금은 최대 하나의 할인 정책과 다수의 할인 조건을 이용해 계산할 수 있다."와 같다.
위 문장은 "금액 할인 정책과 두 개의 순서 조건, 한 개의 기간 조건을 이용해 계산할 수 있다"라는 문장을 포괄할 수 있다는 사실이 중요하다.

추상화를 통해 상위 정책을 기술한다는 것은 기본적인 애플리케이션의 협력 흐름을 기술하다는 것을 의미한다.
영화 예매 가격을 계산하기 위한 흐름은 항상 Movie -> DiscountPolicy -> DiscountCondition 순서로 흘러간다.
할인 정책, 조건의 자식들은 추상화를 이용해서 정의한 흐름에 그대로 따르게 된다.

추상화가 유연한 설계를 가능하게 하는 이유는 설계가 구체적인 상황에 결합되는 것을 방지하기 때문이다.

Movie는 특정한 할인 정책에 묶이지 않는다. 할인 정책을 구현한 클래스가 DiscountPolicy를 상속받고 있다면 어떤 클래스와도 협력이 가능하다.

DiscountPolicy 역시 특정 할인 조건에 묶여있지 않다. DiscountCondition을 상속받은 어떤 클래스와도 협력이 가능하다.

이 모든 것이 **추상화** 덕분이다.

## 📌 유연한 설계
할인 정책이 있는 경우는 모두 살펴봤다.
그런데 **할인 정책이 없는 경우는 어떻게 할까?**

이런 경우에, NoneDiscountPolicy라는 것을 추가하여, 할인정책을 사용하지 않을 경우 , 이 정책을 사용하도록 하자.
```java
public class NoneDiscountPolicy extends DiscountPolicy {
    @Override
    protected Money getDiscountAmount(Screening screening) {
        return Money.ZERO;
    }
}
```
이 정책을 사용하면 Movie 클래스를 수정할 필요도 없고, 기능상 문제 없이 작동 시킬 수 있다.

## 📌 코드 재사용
상속은 코드를 재사용하기 위해 널리 사용되는 방법이다. 그러나 널리 사용되는 방법이라고 해서 가장 좋은 방법은 아니다. 개체지향 설계와 관련된 자료를 본 사람들은 코드 재사용을 위해서는 상속보다는 **합성(composition)**이 더 좋은 방법이라는 이야기를 많이 들었을 것이다.
합성은 다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함해서 재사용하는 방법을 말한다.

다음은 합성 방식을 상속으로 변경한 다이어그램이다.
![](https://velog.velcdn.com/images/pp8817/post/ce1642d8-26c1-4733-b11d-6122183470f2/image.png)

예시로 들자면 Movie가 DiscountPolicy 코드를 재사용하는것이 바로 합성이다.

만약에 이것을 상속으로 사용하고자하면 Movie를 상속받는 AmountdiscountMovie, PercentDiscountMoive를 만들 수 있다.

상속 대신 합성을 선호하는 이유는 무엇일까?

### ✔︎ 상속
상속은 두 가지 관점에서 설계에 안 좋은 영향을 미친다.
- 하나는 상속이 캡슐화를 위반한다는 것이다.
    - 결과적으로 부모 클래스의 구현이 자식 클래스에게 노출되기 때문에 캡슐화가 약화된다.
- 다른 하나는 설계를 유연하지 못하게 만든다는 것이다.
    - 상속은 부모 클래스와 자식 클래스 사이의 관계를 컴파일 시점에 결정한다. 따라서 실행 시점에 객체의 종류를 변경하는 것은 불가능하다.

### ✔︎ 합성
Movie는 요금을 계산하기 위해 DiscountPolicy의 코드를 **재사용**한다.
Movie가 DiscountPolicy의 인터페이스를 통해 약하게 결합되는점을 이용하는 것이다.
Movie는 DiscountPolicy의 calculateDiscountAmount 메시지를 통해 코드를 재사용한다.

이것을 **합성**이라고 한다.

합성은 상속이 가지는 두 가지 문제점을 모두 해결한다.
합성은 인터페이스에 정의된 메시지를 통해서만 재사용이 가능하기 때문에 캡슐화가 가능하며, 의존하는 인스턴스가 쉽게 교체 될 수 있기 때문에 설계가 유연해진다.

>상속을 무조건 사용하지 말라는 것은 아니다. 대부분의 설계에서는 상속과 합성을 적절히 섞어 사용해야한다.
Movie와 DiscountPolicy는 합성으로 연결되어있지만 DiscountPolicy, AmountDiscountPolicy, PercentDiscountPolicy는 상속관계로 되어있다. 이처럼 중복 구현된 코드를 재사용하기 위해서 상속도 적절히 섞어서 사용할 수 있다.

# ⭐️ 느낀점
많은 책들과 글에서 상속은 되도록 피하라고 말한다. 이때문에 상속은 최대한 기피하고 구현, 합성을 활용해야한다는 생각에 사로잡혀있었는데 이번 기회에 생각을 정정할 수 있게 되었다.

비슷하다고 생각하던 **메서드와 메시지**의 차이 또한 넓게 생각하게 되었다.
메서드는 코드의 자율성을 높여주고 메시지는 객체들간의 협력을 위한 수단이라고 생각하게 되었다.

주로 DB에 의존하고, DB 중심적으로 생각했던거 같은데 점점 이런 생각에서 탈피하고 있다.
가장 크게 놀란점은 **유연함**에 대해 언급한 부분이다. 추상화 부분에서 유연하게 만들수록 디버깅이 어려워진다고 말했는데 이 부분은 한 번도 생각해보지 못한 부분이었다. 많은 생각을 하게 된 챕터였다.

- - -
**출처** </br>
[오브젝트 - 코드로 이해하는 객체지향 설계](https://smartstore.naver.com/aladinstores/products/7815204109?NaPm=ct%3Dlsvyemdc%7Cci%3Da772aefc1b71a76e7f3412247dc9108aec75e0f6%7Ctr%3Dboksl%7Csn%3D4399901%7Chk%3D0c624b3c7c58fc3c0cc29d777797044a631fb294) </br>
https://github.com/eternity-oop/object