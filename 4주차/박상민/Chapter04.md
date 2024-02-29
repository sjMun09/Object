# 📕 [Object] 4장. 설계 품질과 트레이드오프 

**가장 중요한 것은 책임이다.**
- 훌륭한 설계란 합리적인 비용 안에서 변경을 수용할 수 있는 구조를 만드는 것이다.
- 훌륭한 설계의 결과로 탄생하는 객체는 결합도가 낮고 응집도가 높다.
- 훌륭한 설계를 위해선 객체의 행동에 초점을 맞춰야 한다. (내부 구현(필드 및 필드 수정 피함)
    - 객체를 단순한 데이터 집합으로 바라보는 시각은 객체의 내부 구현을 퍼블릭 인터페이스에 노출시키는 결과를 낳는다.
    - 즉 내부 변수를 다른 메서드의 인자로 노출시키거나, 다른 객체의 지역 변수로 노출시킨다.
        - 무지성 getter, setter는 지양해야 한다.

**책임을 중심으로 시스템을 후보(역할, 객체, 클래스)로 분할한다.**

# ⭐️ 데이터 중심의 영화 시스템
객체지향 설계는 2가지 방식으로 시스템을 객체로 분할할 수 있다.
- 상태(데이터)를 분할의 중심축으로
- 책임을 분할의 중심축으로

데이터 중심의 관점에서 객체는 자신이 포함하고 있는 데이터를 조작하는데 필요한 오퍼레이션을 정의한다. -> 상태에 초점
책임 중심의 관점에서 객체는 다른 객체가 요청할 수 있는 오퍼레이션을 위해 필요한 상태를 보관한다. -> 책임에 초점

**시스템을 분할하기 위해서는 책임에 초점을 맞춰야 한다.**

객체의 상태를 구현에 속한다. 구현은 불안정 하기 때문에 변하기 쉽다.
**상태**를 분할의 중심축으로 보면, 구현에 관한 세부사항이 객체의 인터페이스에 스며들게 되어 캡슐화의 원칙이 무너진다.

이에 반해 객체의 **책임**은 인터페이스에 속한다.
- 객체는 책임을 드러내는 안정적인 인터페이스 뒤로 책임을 수행하는데 필요한 상태를 캡슐화 함으로써 구현 변경에 대한 파장이 외부로 퍼져나가는 것을 방지한다.

- - -
**순수하게 데이터와 accessot, mutator만 존재하는 클래스들**
![](https://velog.velcdn.com/images/pp8817/post/988b06a3-4d34-48ac-b3e6-c0ac14f0fb9d/image.png)
ReservationAgency 클래스는 데이터 클래스를 조합해서 영화 예매 절차를 구현한다.
```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer,
                               int audienceCount) {
        Movie movie = screening.getMovie();

        boolean discountable = false;
        for(DiscountCondition condition : movie.getDiscountConditions()) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                        condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                        condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
            } else {
                discountable = condition.getSequence() == screening.getSequence();
            }

            if (discountable) {
                break;
            }
        }

        Money fee;
        if (discountable) {
            Money discountAmount = Money.ZERO;
            switch(movie.getMovieType()) {
                case AMOUNT_DISCOUNT:
                    discountAmount = movie.getDiscountAmount();
                    break;
                case PERCENT_DISCOUNT:
                    discountAmount = movie.getFee().times(movie.getDiscountPercent());
                    break;
                case NONE_DISCOUNT:
                    discountAmount = Money.ZERO;
                    break;
            }

            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        } else {
            fee = movie.getFee().times(audienceCount);
        }

        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```
Discountcondition에 대해 루프를 돌면서 할인 가능 여부를 확인한다.
discountable 변수의 값을 체크하고 적절한 할인 정책에 따라 예매 요금을 계산한다.

# ⭐️ 설계 트레이드오프
객체지향 커뮤니티에서는 오랜 기간 동안 좋은 설계의 특징을 판단할 수 있는 기준에 관한 다양한 논의가 있어 왔다. 여기서는 데이터 중심 설계와 책임 중심 설계의 장단점을 비교하기 위해 **캡슐화, 응집도, 결합도**를 사용하겠다.

## 📌 캡슐화
상태와 행동을 하나의 객체 안에 모으는 이유는 내부 구현을 감추기 위해서다.
> 필드가 안드러나게 하기 위함


오직 인터페이스만 밖으로 드러난다. 변경될수 있는 어떤 것이라도 캡슐화해야 한다.

>유지보수성이 목표다. 여기서 유지보수성이란 두려움 없이, 주저함 없이, 저항감 없이 코드를 변경할 수 있는 능력을 말한다. 가장 중요한 동료는 캡슐화다. 캡슐화란 어떤 것을 숨긴다는 것을 의미한다. 우리는 시스템의 한 부분을 다른 부분으로부터 감춤으로써 뜻밖의 피해가 발생할 수 있는 가능성을 사전에 방지할 수 있다. 만약 시스템이 완전히 캡슐화된다면 우리는 변경으로부터 완저히 자유로워질 것이다. 만약 시스템의 캡슐화가 크게 부족하다면 우리는 변경으로부터 자유로울 수 없고, 결과적으로 시스템은 진화할 수 없을 것이다. 응집도, 결합도, 중복 역시 훌륭한(변경 가능한) 코드를 규정하는 데 핵심적인 품질인 것이 사실이지만 캡슐화는 우리를 좋은 코드로 안내하기 때문에 가장 중요한 제1원리다[Bain08].

## 📌 응집도와 결합도
구조적 설계 방법이 주도하던 시대의 기준이나 객체지향의 시대에도 여전히 유효하다.

**응집도** 
- 객체지향 관점에서의 응집도는 객체 또는 클래스에 얼마나 관련 높은 책임들을 할당했는지를 나타낸다.
    - 모듈에 포함된 내부 요소들이 연관돼 있는 정도
    - 모듈 내의 요소들이 하나의 목적을 위해 긴밀하게 협력한다면 그 모듈은 높은 응집도를 가진다.
    - 모듈 내의 요소들이 서로 다른 목적을 추구한다면 그 모듈은 낮은 응집도를 가진다.

**결합도**
- 객체지향의 관점에서 결합도는 객체 또는 클래스가 협력에 필요한 적절한 수준의 관계만을 유지하고 있는지를 나타낸다.
    - 의존성의 정도를 나타내며 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 척도다.
    - 즉, 독립적 변경이 유연함의 정도다.

변경의 관점에서 응집도란 변경이 발생할 때 모듈 내부에서 발생하는 변경의 정도다.
하나의 변경을 수용하기 위해 모듈 전체가 함께 변경된다면 응집도가 높은 것이고, 일부만 변경된다면 응집도가 낮은 것이다.
또한 하나의 변경에 대해 하나의 모듈만 변경되면 응집도가 높지만, 다수의 모듈이 변경되면 응집도가 낮은 것이다.

![](https://velog.velcdn.com/images/pp8817/post/6f6752aa-b973-4670-aa98-215d6298f2cf/image.png)
위 그림은 변경과 응집도 사이의 관계를 그림을 표편한 것이다.
**높은 응집도(High Cohesion)**
- 하나의 요규사항 변경을 반영하기 위해 오직 하나의 모듈만 수정하면 된다.

**낮은 응집도(Low Cohesion)**
- 하나의 원인에 의해 변경해야 하는 부분이 다수의 모듈에 분산돼 있기 때문에 여러 모듈을 동시에 수정해야 한다.

응집도가 높을수록 변경의 대상과 범위가 명확해지기 때문에 코드를 변경하기 쉬워진다.

![](https://velog.velcdn.com/images/pp8817/post/8ad05b2c-5f48-40ca-9ee0-0598d6539d36/image.png)
결합도 역시 변경의 관점에서 설명할 수 있다. 결합도는 **한 모듈이 변경되기 위해서 다른 모듈의 변경을 요구하는 정도로 측정할 수 있다.**

**낮은 결합도(Low Coupling)**
- 모듈 A를 변경했을 때 오직 하나의 모듈만 영향을 받는다.

**높은 결합도(High Coupling)**
- 모듈 A를 변경했을 때 4개의 모듈을 동시에 변경해야 한다.


# ⭐️ 데이터 중심 영화 예매 시스템의 문제점
데이터 중심의 설계는 캡슐화를 위반하고 객체의 내부 구현을 인터페이스의 일부로 만든다.
반면, 책임 중심의 설계는 객체의 내부 구현을 안정적인 인터페이스 뒤로 캡슐화한다.

**캡슐화의 정도가 객체의 응집도와 결합도를 결정한다는 사실을 기억해야 한다.**

**데이터 중심의 설계가 가진 대표적인 문제점들**
- 캡슐화 위반
- 높은 결합도
- 낮은 응집도

## 📌 캡슐화 위반
```
public class Movie {
	private Money fee;
    
    public Money getFee() {
    	return fee;
    }
    
    public void setFee(Money fee) {
    	this.fee = fee;
    }
}
```
get, set 메서드는 Movie 내부에 Money 타입의 fee라는 이름의 인스턴스 변수가 존재한다는 사실을 퍼블릭 인터페이스에 노골적으로 드러낸다.

Movie가 캡슐화의 원칙을 어기게 된 근본적인 원인은 객체가 수행할 책임이 아니라 내부에 저장할 데이터에 초점을 맞췄기 때문이다. 객체에 중요한 것은 책임이다. 그리고 구현을 캡슐화할 수 있는 적절한 책임은 협력이라는 문맥을 고려할 때만 얻을 수 있다.

## 📌 높은 결합도
지금까지 살펴본 것처럼 데이터 중심의 설계는 접근자와 수정자를 통해 내부 구현을 인터페이스의 일부로 만들기 때문에 캡슐화를 위반한다. 객체 내부의 구현이 객체의 인터페이스에 드러난다는 것은 클라이언트가 구현에 강하게 결합된다는 것을 의미한다.
그리고 더 나쁜 소식은 **단지 객체의 내부 구현을 변경했음에도 이 인터페이스에 의존하는 모든 클라이언트들도 함게 변경해야 한다는 것이다.**

![](https://velog.velcdn.com/images/pp8817/post/7def9652-3e7d-4f80-87b9-43e32555c5b8/image.png)
위 그림을 살펴보면 대부분의 제어 로직을 가지고 있는 제어 객체인 ReservationAgency가 모든 데이터 객체에 의존한다는 것을 알 수 있다. DiscountCondition의 데이터가 변경되면 DiscountCondition뿐만 아니라 ReservationAgency도 함께 수정해야 한다.

## 📌 낮은 응집도
**서로 다른 이유로 변경되는 코드가 모듈 안에 공존하면 응집도가 낮다.**
코드를 수정하는 이유를 찾아보자

**ReservationAgency가 변경되는 이유**
- 할인정책 추가
- 할인 정책별 계산 로직 변경
- 할인 조건 추가
- 예매 요금 계산 방식 변경
- 할인 조건별 할인 여부 판단 방법 변경

낮은 응집도는 두가지 측면에서 설계에 문제를 일으킨다
- 변경의 원인이 다른 코드들이 하나의 모듈 안에 뭉쳐있어 변경과 아무 상관 없는 코드들이 영향을 받는다.
    - 할인 정책을 추가하는 코드가 할인 조건을 판단하는 코드에 영향을 미칠 수 있다.

- 하나의 요구사항 변경을 위해 여러 모듈을 동시에 수정해야 한다.
    - 할인 정책이 추가되면 3개의 모듈이 동시에 변경된다.
        - MoneyType enum 열거형 값 추가
        - ReservationAgency Switch에 case 추가
        - Movie에 새로운 할인 정책 위해 필요한 데이터 추가


![](https://velog.velcdn.com/images/pp8817/post/196eb875-487f-4a56-8c1e-03c9bea7886d/image.png)

# ⭐️ 자율적인 객체를 향해
## 📌 캡슐화를 지켜라
캡슐화는 설계의 제 1원리다. 데이터 중심의 설계가 낮은 응집도와 높은 결합도라는 문제로 몸살을 앓게 된 근본적인 원인은 바로 캡슐화의 원칙을 위반했기 때문이다. 객체는 자신이 어떤 데이터를 가지고 있는지를 내부에 캡슐화하고 외부에 공개해서는 안된다. 객체는 스스로의 상태를 책임져야 하며 외부에서는 인터페이스에 정의된 메서드를 통해서만 상태에 접근할 수 있어야 한다.

**예제: 사각형의 너비의 높이를 증가시키는 코드**
```java
class AnyClass{
	void anyMethod(Rectangle rectangle, int multiple){
    rectangle.setRight(rectangle.getRight()*multiple)
    rectangle.setBottom(rectangle.getBottom()*multiple)
   }

}
```
**해당 코드의 문제점**
- 코드 중복: 다른 곳에서 비슷한 코드를 구현할 수 있다.
- 변경에 취약: rectangle 가로세로 => 4개 좌표로 내부 구현 바뀌면 위 코드는 전면적으로 바뀌어야 함.

**해결 방법: 캡슐화를 강화시키자**
```java
class Rectangle{
   public void enlarge(int multiple){
   		right *= multiple;
        bottom *= multiple; 
   }
}
```
Rectangle 내부에 너비와 높이를 조절하는 로직을 캡슐화하면 두 가지 문제를 해결할 수 있다.

## 📌 스스로 자신의 데이터를 책임지는 객체
- 객체 내부 데이터보다 객체가 협력하며 수행할 책임을 정의하는 오페레이션이 더 중요하다.

객체를 설계할 때 "이 객체가 어떤 데이터를 포함해야 하는가?"라는 질문은 다음과같은 두 개의 개별적인 질문으로 분리해야 한다.
- 이 객체가 어떤 데이터를 포함해야 하는가?
- 이 객체가 데이터에 대해 수행해야 하는 오퍼레이션은 무엇인가?

이전의 설계를 수정해보자.
- 내부 구현을 더 면밀하게 캡슐화 시키자.
- 데이터를 처리하는 데 필요한 메서드를 데이터를 가지고 있는 객체 스스로 구현하게 만들자.

따라서 이 객체들이 스스로 책임지도록 만들자.

**첫번째 설계**
![](https://velog.velcdn.com/images/pp8817/post/397f172c-8ac5-4bdd-830a-2de93da6e4ff/image.png)

**두번째 설계**
![](https://velog.velcdn.com/images/pp8817/post/fbd8c185-d5d2-44d6-93e4-737f68ad18c8/image.png)
두 설계를 비교해보자. 무엇이 더 객체지향적으로 보이는가?
최소한 결합도 측면에서 ReservationAgency에 의존성이 몰려있던 첫 번째 설계보다는 두 번째 설계가 객체지향적으로 보인다.

# ⭐️ 하지만 여전히 부족하다.
사실 두 번째 설계 역시 데이터 중심의 설계 방식에 속한다고 할 수 있다.
그 이유를 알아보자.

## 📌 캡슐화 위반
분명히 수정된 객체들은 자기 자신의 데이터를 스스로 처리한다. 예를 들어 DiscountCondition은 자기 자신의 데이터를 이용해 할인 가능 여부를 스스로 판단한다.

DiscountCondition은 내부 인스턴스 변수의 타입을 인터페이스로 외부에 노출한다.
- sequence, dayOfWeek ...

Movie는 할인 정책의 종류(case, if)를 인터페이스에 노출시키고 있다.
- Screening에서 Movie의 getType를 이용해서 switch
- 새로운 할인 정책이 추가되거나 제거되면 Screening을 변경해야 한다.

> **캡슐화의 진정한 의미**
사실 캡슐화는 변경될 수 있는 어떤 것이라도 감추는 것을 의미한다. 내부 속성을 외부로부터 감추는 것은 '데이터 캡슐화'라고 불리는 캡슐화의 한 종류일 뿐이다.
진정한 캡슐화는 속성 타입이건, switch문의 case이건 관계없이 구현과 관련된 것은 모두 감추는 것이다.

## 📌 높은 결합도
사용 객체 내부의 type을 꺼내서 switch하는 경우, 내부 type의 케이스가 변경되면 사용처를 바꿔줘야 한다.
- DiscountCondition 변경 => Movie 변경

DiscountCondition의 만족 여부 판단 정보가 변경되면 isDiscountable의 메서드 파라미터도 변경해야 한다. (whenScreened,sequence 둘 다 쓰고 있음)
- 이 정보는 Screening에서 Movie를 통해 전달

DiscountCondition 변경 => Movie 변경 => Screening 변경

**하나의 변경을 수용하기 위해 여러 모듈을 동시에 변경해야 한다.**

## 📌 낮은 응집도
DiscountCondition이 할인 여부 판단에 필요한 정보가 변경되면
- Movie의 isDiscountable 파라미터 종류를 변경 -> Screening의 Movie 호출 변경

하나의 변경을 수용하기 위해 여러 곳(내부의 여러 메소드, 다른 모듈)을 동시에 변경해야 한다.
 
**다시 말하지만 내부 구현(DiscountCondition의 필드)이 노출되었기 때문이다.**

# ⭐️ 데이터 중심 설계의 문제점
데이터 중심의 설계가 변경에 취약한 이유 두 가지
- 데이터 중심의 설계는 본질적으로 너무 이른 시기에 데이터에 관해 결정하도록 강요한다.
- 데이터 중심의 설계에서는 협력이라는 문맥을 고려하지 않고 객체를 고립시킨 채 오퍼레이션을 결정한다.

## 📌 데이터 중심 설계는 객체의 행동보다는 상태에 초점을 맞춘다
데이터 중심의 설계를 시작할 때 던졌던 첫 번째 질문은 "이 객체가 포함해야 하는 데이터가 무엇인가?"다. 데이터는 구현의 일부이다.

데이터 주도 설계는 시작하는 처음부터 데이터에 관해 결정하도록 강요하기 때문에 너무 이른 시기에 내부 구현에 초점을 맞춘다.

데이터 중심의 관점에서 객체는 그저 단순한 데이터의 집합체일 뿐이다. 이로 인해 접근자와 수정자를 과도하게 추가하게 되고 이 데이터 객체를 사용하는 절차를 분리된 별도의 객체 안에 구현하게 된다.

결론적으로 데이터 중심의 설계는 너무 이른 시기에 데이터에 대해 고민하기 때문에 캡슐화에 실패하게 된다. 객체의 내부 구현이 객체의 인터페이스를 어지럽히고 객체의 응집도와 결합도에 나쁜 영향을 미치기 때문에 변경에 취약한 코드를 낳게 된다.

## 📌 데이터 중심 설계는 객체를 고립시킨 채 오퍼레이션을 정의하도록 만든다.
객체지향 애플리케이션을 구현하다는 것은 협력하는 객체들의 공동체를 구축한다는 것을 의미한다. 따라서 협력이라는 문맥 안에서 필요한 책임을 결정하고 이를 수행할 적절한 객체를 결정하는 것이 가장 중요하다.
올바른 객체지향 설계의 무게 중심은 항상 객체의 내부가 아니라 외부에 맞춰져 있어야 한다.


- - -
**출처** <br>
[오브젝트 - 코드로 이해하는 객체지향 설계](https://smartstore.naver.com/aladinstores/products/7815204109?NaPm=ct%3Dlsvyemdc%7Cci%3Da772aefc1b71a76e7f3412247dc9108aec75e0f6%7Ctr%3Dboksl%7Csn%3D4399901%7Chk%3D0c624b3c7c58fc3c0cc29d777797044a631fb294) <br>
https://github.com/eternity-oop/object









