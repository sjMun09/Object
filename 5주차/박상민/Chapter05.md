# 📕 [Object] 5장. 책임 할당하기

# ⭐️ 책임 주도 설계를 향해
데이터 중심의 설계에서 책임 중심의 설계로 전환하기 위해서는 다음의 두 가지 원칙을 따라야 한다.
- 데이터보다 행동을 먼저 결정하라.
- 협력이라는 문맥 안에서 책임을 결정하라.

## 📌 데이터보다 행동을 먼저 결정하라
객체에게 중요한 것은 데이터가 아니라 외부에 제공하는 행동이다. 클라이언트의 관점에서 객체가 수행하는 행동이란 곧 객체의 책임을 의미한다. 객체는 협력에 참여하기 위해 존재하며 협력 안에서 수행하는 책임이 객체의 존재가치를 증명한다.

객체지향 설계에서 가장 중요한 것은 적절한 객체에게 적절한 책임을 할당하는 능력이다. 그렇다면 객체에게 어떤 책임을 할당해야 하는가?
해결의 실마리를 협력에서 찾을 수 있다.

## 📌 협력이라는 문맥 안에서 책임을 결정하라
객체에게 할당된 책임의 품질은 협력에 적합한 정도로 결정된다.
메시지를 전송하는 클라이언트의 의도에 적합한 책임을 할당해야 한다. 협력에 적합한 책임을 수확하기 위해서는 객체를 결정한 후에 메시지를 선택하는 것이 아니라 메시지를 결정한 후에 객체를 선택해야 한다. 메시지가 존재하기 때문에 그 메시지를 처리할 객체가 필요한 것이다.
객체가 메시지를 선택하는 것이 아니라 메시지가 객체를 선택하게 해야 한다.

![](https://velog.velcdn.com/images/pp8817/post/d0b967c6-af0d-4548-b521-132ad5ad10e5/image.png)

메시지는 클라이언트의 의도를 표현한다.
메시지를 먼저 표현하면, 수신자에 대한 어떠한 가정에서도 자유로워진다. (깔끔한 캡슐화)

## 📌 책임 주도 설계
- 시스템이 사용자에게 제공해야 하는 기능인 시스템 책임을 파악한다
- 시스템 책임을 더 작은 책임으로 분할한다
- 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당한다
- 객체가 책임을 수행하는 도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾는다
- 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 된다

# ⭐️ 책임 할당을 위한 GRASP 패턴
GRASP(General Responsibility Assign Software Pattern) 패턴은 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 원칙들의 집합을 패턴 형식으로 정리한 것이다.

## 📌 도메인 개념에서 출발하기
책임 할당 시 가장 먼저 고민해야 하는 것은 도메인 개념이다.
- 도메인: 소프트웨어가 해결하고자 하는 문제

![](https://velog.velcdn.com/images/pp8817/post/8b89dc9a-ea88-49be-9c49-e6d97a8f3f84/image.png)
위 그림은 하나의 영화는 여러번 상영될 수 있으며, 하나의 상영은 여러번 예약될 수 있다는 것을 나타낸다. 할인 정책과 할인 조건은 각각 2개이며, 할인 조건은 여러개일 수 있지만 할인 정책은 하나만 가능하다.

>설계 시작 단계에서는 개념들의 의미나 관계가 완벽할 필요가 없다. 우리에게는 출발점이 필요할 뿐이다.
도메인 개념과 관계는 구현의 기반이 된다.

## 📌 정보 전문가에게 책임을 할당하라
책임 주도 설계 방식의 첫 단계는 애플리케이션이 제공해야 하느 기능을 애플리케이션의 책임으로 생각하는 것이다.

이 책임을 애플리케이션에 대해 전송된 메시지로 간주하고 이 메시지를 책임질 첫번째 객체를 선택하는 것으로 설계를 시작한다.

**첫 번째 질문**
- 메세지를 전송할 객체는 무엇을 원하는가?

협력을 시작하는 객체는 미정이지만 이 객체가 원하는 것은 분명해 보인다. 바로 영화를 예매하는 것이다.
![](https://velog.velcdn.com/images/pp8817/post/712f3d55-dd22-4e1c-bc74-4ea09ee4cc9f/image.png)
협력을 시작하는 객체는 미정이지만 이 객체가 원하는 것은 분명하다.

**두 번째 질문**
- 메시지를 수신할 적합한 객체는 누구인가?

이 질문에 답하기 위해서는 객체가 상태와 행동을 통합한 캡슐화의 단위라는 사실에 집중해야 한다.

객체에게 책임을 할다아는 첫 번째 원칙은 책임을 수행할 정보를 알 고 있는 객체에게 책임을 할당하는 것이다. GRASP에서는 INFORMATION EXPERT(정보 전문가) 패턴이라고 부른다.
**INFORMATION EXPERT 패턴은 객체가 자율적인 존재여야 한다는 것을 상기시킨다.**
> 정보를 알고있는 객체만이 책임을 어떻게 수행할지 스스로 결정할 수 있다.정보와 행동이 가까운데 뭉쳐 캡슐화가 유지되며, 정보별로 책임이 분산되어 응집도가 높아지고 결합도가 낮아진다.또한 가독성이 높아진다.

알고있다는 것은 꼭 필드(저자된 데이터)일 필요는 없다. 정보는 제공할 수 있는 다른 객체를 알거나 계산해서 제공할 수 있다.
![](https://velog.velcdn.com/images/pp8817/post/4dbdd2c0-9734-4ea7-871a-db827a50bffe/image.png)
상영은 상영 순번, 상영 시간, 영화 정보 등의 가장 많은 정보를 알고 있다.

![](https://velog.velcdn.com/images/pp8817/post/cdf88185-9c3b-4582-af88-853222f91af4/image.png)
상영은 영화 가격을 모르기 때문에(캡슐화) 계산을 요청한다.

![](https://velog.velcdn.com/images/pp8817/post/b0dca230-9b5e-4729-86fe-c9e1a1de1557/image.png)
영화는 할인 여부 판단을 못하므로 할인 여부 판단을 요청한다.

![](https://velog.velcdn.com/images/pp8817/post/ce30dc9f-33a5-4efb-8632-d4259363df21/image.png)
영화는 할인 정책을 알고 있으므로 할인 정책에 따라 요금을 계산해서 반환한다.


## 📌 높은 응집도와 낮은 결합도
설계는 트레이드오프 활동이다.
몇 가지 설계 중 하나를 선택해야 하는 경우 INFORMATION EXPERT 패턴 이외의 다른 책임 할당 패턴들을 함께 고려할 필요가 있다.
![](https://velog.velcdn.com/images/pp8817/post/67b9ddba-a3a2-4470-99dd-5f6ff70a62da/image.png)
**반대로 상영이 직접 할인조건이랑 협력한다면?**


>**LOW COPLING 패턴**
설계의 전체적인 결합도가 낮게 유지되도록 책임을 할당하라.
상영이 2개와 협력하는 두번째 구조는 결합도가 높아져 좋지 앉은 설계이다.


>**HIGH COHESION 패턴**
높은 응집도를 유지할 수 있게 책임을 할당하라.
두번째 구조는 Screen에게 영화 요금 계산 책임이 분산된다. 즉 Screen의 책임이 늘어난다.
이는 같이 변경될 여지도 증가한다.
첫번째 구조에서 Movie의 책임은 가격 계산이기 때문에, 할인조건과의 협력은 응집도 높은 설계이다.

## 📌 창조자 객체에게 객체 생성 책임을 할당하라
GRASP의 CREATOR(창조자) 패턴은 이 같은 경우에 사용할 수 있는 책임 할당 패턴으로서 객체를 생성할 책임을 어떤 객체에게 할당할지에 대한 지침을 제공한다.

>**CREATOR 패턴**
아래 조건을 최대한 많이 만족하는 B에게 객체 생성 책임을 할당하라.
- B가 A 객체를 포함하거나 참조한다
- B가 A 객체를 기록한다
- B가 A 객체를 긴밀하게 사용한다
- B가 A 객체를 초기화하는데 필요한 데이터를 가지고 있다.(이 경우 B는 A에 대한 정보 전문가다)

연관을 필요로 하는 객체에 생성 책임을 맡긴다. 필요로 한다면 어떻게든 결합해야 하기 때문이다.
이미 결합되어있는 객체가 생성하는 것은 결합도가 변하지 않는다.
![](https://velog.velcdn.com/images/pp8817/post/3d766407-66ae-4e3d-ba17-28c252138f0e/image.png)

Screening은 Reservation을 생성하는데 필요한 가장 많은 정보를 알고 있다. (정보 전문가)


## 📌 DiscountCondition 개선하기
가장 큰 문제점은 변경에 취약한 클래스를 포함하고 있다는 것이다. 변경에 취약한 클래스란 코드를 수정해야 하는 이유를 하나 이상 가지는 클래스이다.

```java
public class DiscountCondition {
    private DiscountConditionType type;
    private int sequence;
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public boolean isSatisfiedBy(Screening screening) {
        if (type == DiscountConditionType.PERIOD) {
            return isSatisfiedByPeriod(screening);
        }

        return isSatisfiedBySequence(screening);
    }

    private boolean isSatisfiedByPeriod(Screening screening) {
        return dayOfWeek.equals(screening.getWhenScreened().getDayOfWeek()) &&
                startTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                endTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0;
    }

    private boolean isSatisfiedBySequence(Screening screening) {
        return sequence == screening.getSequence();
    }
}
```
DiscountCondition은 다음과 같이 서로 다른 세 가지 이유로 변경될 수 있다.

- **새로운 할인 조건 추가**
    - issatisfiedBy 메서드 안 if~else 구문 수정
    - DiscountCondition에 속성 추가


- **순번 조건 판단 로직 변경**

- isSatisfiedBySequence 내부 구현 수정
- DiscountCondition의 sequence 속성 변경


**기간 조건 판단 로직 변경**
- isSatisfiedByPeriod 메서드 내부 구현 수정
- dayOfWeek, startTime, endTime 변경

즉, DiscountCondition 클래스는 하나 이상의 변경 이유를 가지기 때문에 응집도가 낮다.
변경의 이유에 따라 클래스를 분리해야 한다. 
서로 다른 시점, 서로 다른 이유로 변경되는 코드들을 분리한다.


## 📌 코드를 통해 변경의 이유 파악하기
인스턴스 변수가 초기화되는 시점.
- 응집도가 높은 클래스는 모든 속성을 함께 초기화한다.
- 일부가 null로 남겨지는 클래스나, 나중에 set되는 클래스는 의심해본다.


위의 사례에서는 sequence, dayOfWeek, startTime, endTime
-> 함께 초기화되는 속성을 기준으로 코드를 분리한다.

메서드들이 인스턴스 변수를 사용하는 방식
- 메소드가 모든 속성을 사용한다면 응집도가 높다
- 메서드들이 사용하는 속성에 따라 그룹이 나뉜다면 클래스의 응집도가 낮다

![](https://velog.velcdn.com/images/pp8817/post/61dc0192-842a-4ad1-bf65-e136a61e7892/image.png)

### 📌 해결 방법 1: 타입 분리하기
DiscountCondition의 가장 큰 문제는 순번 조건과 기간 조건이라는 두개의 독립적인 타입이 하나의 클래스 안에 공존하고 있다는 점이다.
해결 방법은 두 타입을 SequenceCondition, PeriodCondition 두 개의 클래스로 분리하는 것이다.
- sequence 속성만 사용하는 메서드 => SequenceCondition 이동
- dayOfWeek, startTime, endTime => PeriodCondition 이동

모든 메서드가 동일한 인스턴스 변수 그룹을 사용하도록 하여 응집도를 높인다.

![](https://velog.velcdn.com/images/pp8817/post/841bcd96-ce22-4804-b4a9-e130171bedd5/image.png)
**이제 Movie는 두 클래스와 협력한다.**

그러나 이 해결 방법은
- 아는 클래스가 2개로 증가하여 결합도가 높아짐
- 목록 별로 넣는 로직이 필요해 구현이 귀찮아짐

의 문제점을 가지고 있다.

### 📌 해결방법 2: 다형성을 통해 분리
Movie 입장에서 둘은 할인 여부를 판단하는 **동일한 책임을 수행**할 뿐이다.
즉, **동일 책임을 수행하는 동일한 역할**을 한다.
![](https://velog.velcdn.com/images/pp8817/post/485094cd-b149-47f3-ba1a-5e68dc388e32/image.png)


객체의 타입에 따라 변하는 행동이 있다면 (if) 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당하면 된다.
![](https://velog.velcdn.com/images/pp8817/post/d6f14d9c-c14e-4ea0-ab41-fc0bd81ae47d/image.png)

객체의 암시적인 타입에 따라 행동을 분기해야 한다면 암시적인 타입을 명시적 클래스로 정의하고 행동을 나눔으로써 응집도 문제를 해결할 수 있다.
다시 말해 객체의 타입의 따라 변하는 행동이 있다면 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당하라는 것이다. GSASP에서는 이를 **POLYMORPHISM(다형성) 패턴**이라고 부른다.

>**POLYMORPHISM 패턴 : 타입별 분리**
객체의 타입에 따라 변하는 로직이 있을 때, 변하는 로직을 담당할 책임을 어떻게 할당해야 하는가?
타입을 명시적으로 정의하고 각 타입에 다형적으로 행동하는 책임을 할당하라.
if~else 또는 switch-case등의 조건 논리를 사용해서 설계한다면 새로운 변화가 일어난 경우 조건 논리를 수정해야 한다.
이것은 프로그램을 수정하기 어렵고 변경에 취약하게 만든다.
객체의 타입을 검사해서 타입에 따라 여러 대안들을 수행하는 조건적인 논리를 사용하지마라.


## 📌 Movie 클래스 개선하기
Movie 또한 DiscountDondition과 동일한 문제를 가지고 있다. 금액 할인 정책과 비율 할인 정책이라는 두 가지 타입을 하나의 클래스 안에 구현하고 있기 때문에 하나 이상의 이유로 변경될 수 있다. 한마디로 말해서 **응집도가 낮은 것**이다.

해결 방법 역시 DiscountCondition과 동일하다. 역할의 개념을 도입해서 협력을 다형적으로 만들면 된다.
POLYMORPHISM 패턴을 사용해 서로 다른 행동을 타입별로 분리하면 다형성의 혜택을 누릴 수 있다.
```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;

    private MovieType movieType;
    private Money discountAmount;
    private double discountPercent;

    public Money calculateMovieFee(Screening screening) {
        if (isDiscountable(screening)) {
            return fee.minus(calculateDiscountAmount());
        }

        return fee;
    }

    private boolean isDiscountable(Screening screening) {
        return discountConditions.stream()
                .anyMatch(condition -> condition.isSatisfiedBy(screening));
    }

    private Money calculateDiscountAmount() {
        switch(movieType) {
            case AMOUNT_DISCOUNT:
                return calculateAmountDiscountAmount();
            case PERCENT_DISCOUNT:
                return calculatePercentDiscountAmount();
            case NONE_DISCOUNT:
                return calculateNoneDiscountAmount();
        }

        throw new IllegalStateException();
    }

    private Money calculateAmountDiscountAmount() {
        return discountAmount;
    }

    private Money calculatePercentDiscountAmount() {
        return fee.times(discountPercent);
    }

    private Money calculateNoneDiscountAmount() {
        return Money.ZERO;
    }
}
```
calculateDiscountAmout함수를 보니 switch가 보인다.
이는 두가지 할인 정책을 하나의 클래스 안에서 구현하고 있기 때문이다.
POLYMOPHISM 패턴으로 타입을 분리한다.
PROTECTED VARIATIONS 패턴을 이용해 Movie내의 속성들을 분리한다.
![](https://velog.velcdn.com/images/pp8817/post/cdb68bc5-9aa0-47d2-8378-630a61b5bd01/image.png)
모든 클래스의 내부 구현은 캡슐화돼 있고 모든 클래스는 변경의 이유를 오직 하나씩만 가진다. 각 클래스는 응집도가 높고 다른 클래스와 최대한 느슨하게 결합돼 있다. 클래스는 작고 오직 한 가지 일만 수행한다. 책임은 적절하게 분리돼 있다. 이것이 책임을 중심으로 협력을 설계할 때 얻을 수 있는 혜택이다.

결론은 데이터가 아닌 책임을 중심으로 설계하라는 것이다. 객체에게 중요한 것은 상태가 아니라 행동이다. 객체지향 설계의 기본은 책임과 협력에 초점을 맞추는 것이다.


## 📌 변경과 유연성
개발자가 변경에 대비하는 방법
- 코드를 이해하고 수정하기 쉽게 단순하게 설계
- 코드를 수정하지 않고 변경을 수용할 수 있도록 코드를 더 유연하게 만드는 것

대부분의 경우에 전자가 더 좋은 방법이지만 유사한 변경이 반복적으로 발생하고 있다면 복잡성이 상승하더라도 유연성을 추가하는 두 번째 방법이 더 좋다.
![](https://velog.velcdn.com/images/pp8817/post/5c2d1802-d04c-4dfa-8b20-722612c2574d/image.png)

유연성은 의존성 관리의 문제다.
요소들 사이의 의존성의 정도가 유연성의 정도를 결정한다.
유연성의 정도에 따라 결합도를 조절할 수 있는 능력은 객체지향 개발자가 갖춰야 하는 중요한 기술 중 하나다.
![](https://velog.velcdn.com/images/pp8817/post/93d610a6-9790-41a5-9057-22bb765f1e19/image.png)

# ⭐️ 결론
**응집도**
- 한 객체 내의 필드를 모든 메소드에서 다 사용하나?
    - 아닌 메소드가 있으면 분리를 생각해보자.
- 객체 내 초기화되는 시점이 다른 변수가 있다면 분리를 생각해보자.
- 하나 이상의 이유로 변경된다면 분리를 생각한다. (switch-if, 다른 객체의 필드 노출)

**결합도** : 최대한 적은 객체들과 협력하면 좋다.
**캡슐화** : 내가 갖고 있는 필드를 안과 밖에서 숨겨라. (파라미터, getter, setter)

- - -
**출처** <br>
[오브젝트 - 코드로 이해하는 객체지향 설계](https://smartstore.naver.com/aladinstores/products/7815204109?NaPm=ct%3Dlsvyemdc%7Cci%3Da772aefc1b71a76e7f3412247dc9108aec75e0f6%7Ctr%3Dboksl%7Csn%3D4399901%7Chk%3D0c624b3c7c58fc3c0cc29d777797044a631fb294) <br>
https://github.com/eternity-oop/object












