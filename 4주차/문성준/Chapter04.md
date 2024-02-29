객체지향 설계의 핵심은 **협력, 역할, 책임**이다. 이 중에서도 가장 중요한 것이 '**책임**'이다.
**책임은 객체지향 애플리케이션의 품질을 결정한다.**

객체지향 설계란 올바른 객체에게 올바른 책임을 할당하면서 낮은 결합도와 높은 응집도를 가진 구조를 창조하는 것이다. 즉, 객체지향 설계의 핵심이 책임이고 책임을 할당하는 과정이 응집도, 결합도와 연관되어있다.

설계라는 것은 변경을 위해 존재하고 변경 시에는 반드시 비용이 발생한다. 훌륭한 설계란 적절한 비용 안에서 쉽게 변경할 수 있는 응집도가 높고 서로 느슨하게 결합된 요소로 구성된 설계이다.

그렇다면 응집도를 높이기 위해선 어떡하면 되는 것일까???

객체의 상태가 아니라 **객체의 행동에 초점**을 두면 된다. 
다시 말해 객체의 책임에 초점을 두는 것이다. (이는 데이터 중심 설계와 반대된다.)

---

### **데이터 중심 설계 - DDD**

설계가 필요한 이유는 요구사항이 변경되기 때문이다. 그런데 데이터 중심의 설계는 변경에 취약하다. 객체의 상태(=데이터)는 구현에 속한다. 그런데 구현은 불안정하기 때문에 변하기 쉽다. 이러한 상태의 변경은 인터페이스의 변경을 초래하고 해당 인터페이스에 의존하는 모든 객체에 영향이 퍼지게 된다. 따라서 데이터 중심의 설계는 변경에 취약하다.

반면에, 책임 주도 설계는 상대적으로 변경에 안정적인 설계를 얻을 수 있다. 책임은 인터페이스에 속한다. 안정적인 인터페이스 뒤에 상태를 캡슐화함으로써 구현 변경에 대한 파급효과를 방지한다. 따라서 책임 주도 설계는 변경에 안정적이다.

```java
import java.util.List;

public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;// 할인 조건의 목록 직접 포함private MovieType movieType;// 할인 정책의 종류 결정private Money discountAmount;// 할인 조건 직접 정의private double discountPercent;// 할인 조건 직접 정의
}
```

```java
// 할인 정책의 종류를 결정한다public enum MovieType {
    AMOUNT_DISCOUNT,// 금액 할인 정책
    PERCENT_DISCOUNT,// 비율 할인 정책
    NONE_DISCOUNT// 미적용
}
```

데이터 중심의 설계는 객체가 내부에 저장해야 하는 '**데이터가 무엇인가**'부터 시작된다. 그러다 보니 Movie 안에 **직접 정의**되는 차이들이 발생했다. 데이터 중심 설계에서는 이처럼 **객체의 종류를 저장**하는 인스턴스 변수와 **인스턴스 종류에 따라 배타적으로 사용될 인스턴스 변수를 하나의 클래스에 포함**시키는 방식이 사용된다.

그런데, 객체지향에서 가장 중요한 원칙은 **캡슐화**이다. 따라서 캡슐화를 지키기 위해 **접근자**와 **수정자**를 추가한다.

```java
import java.util.Collections;
import java.util.List;

public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;// 할인 조건의 목록private MovieType movieType;// 할인 정책의 종류 결정private Money discountAmount;// 할인 조건 직접 정의private double discountPercent;// 할인 조건 직접 정의public MovieType getMovieType() {
        return movieType;
    }

    public void setMovieType(MovieType movieType) {
        this.movieType = movieType;
    }

    public Money getFee() {
        return fee;
    }

    public void setFee(Money fee) {
        this.fee = fee;
    }

    public List<DiscountCondition> getDiscountConditions() {
        return Collections.unmodifiableList(discountConditions);
    }

    public void setDiscountConditions(List<DiscountCondition> discountConditions) {
        this.discountConditions = discountConditions;
    }

    public Money getDiscountAmount() {
        return discountAmount;
    }

    public void setDiscountAmount(Money discountAmount) {
        this.discountAmount = discountAmount;
    }

    public double getDiscountPercent() {
        return discountPercent;
    }

    public void setDiscountPercent(double discountPercent) {
        this.discountPercent = discountPercent;
    }
}
```

---

### **설계 트레이드오프**

지금 데이터 중심 설계를 하는 이유는 책임 주도 설계와의 장단점을 비교하기 위해서다. 이를 판단하기 위한 척도로 캡슐화, 응집도, 결합도를 사용한다.

### **캡슐화**

캡슐화는 외부에서 알 필요가 없는 부분을 감춤으로써 대상을 단순화하는 추상화의 한 종류다. 객체지향은 **불안정한 구현**의 세부사항을 **안정적인 인터페이스**의 뒤로 캡슐화한다.

**설계**는 요구사항이 **변경되기 때문에** 필요하다. **캡슐화**는 **변경으로 인한 파급효과를 통제**하기 위해 사용한다. 따라서 변경의 관점에서 설계의 품질을 판단하기 위해 캡슐화를 기준으로 삼는다. 그리고 객체지향 설계의 핵심은 **변경될 수 있는 어떤 것이라도** 캡슐화해야 한다는 것이다.

### **응집도와 결합도**

**응집도**는 모듈에 포함된 내부 요소들이 연관된 정도를 나타낸다.모든 모듈의 요소들이 하나의 목적을 위해 협력한다면 높은 응집도를 가진다.

**결합도**는 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 척도이다.

좋은 설계는 높은 응집도와 느슨한 결합도를 가져야 한다. 좋은 설계는 오늘의 기능을 수행하면서 내일의 변경을 수용할 수 있어야 하는데 높은 응집도와 낮은 결합도는 설계를 변경하기 쉽게 해 주기 때문이다.

응집도와 결합도는 **변경**과 관계가 있다. 변경의 관점에서 응집도란, **변경이 발생할 때 모듈 내부에서 발생하는 변경의 정도**로 측정 가능하다.

!https://blog.kakaocdn.net/dn/b9FA5x/btrqMQyI1Bh/XRnBaTnySmJkDgSlRtSkmk/img.png

**응집도가** 높을수록 변경의 대상과 범위가 명확해지기 때문에 변경이 쉬워진다. 변경을 반영하기 위해선 오직 하나의 모듈만 수정하면 된다.

!https://blog.kakaocdn.net/dn/lEt0L/btrqMk0W1RZ/Uky8mYFcveTZKK2gGrf1Hk/img.png

**결합도**는 한 모듈이 변경하기 위해서 다른 모듈의 변경을 요구하는 정도로 측정할 수 있다. 결합도가 높으면 함께 변경해야 하는 모듈의 수가 늘기 때문에 변경이 어려워진다.

**결합도**는 **변경의 원인**으로도 설명이 가능하다. 1) 내부 구현을 변경했을 때, 결합도가 높다. 2) 퍼블릭 인터페이스를 변경했을 때 결합도가 낮다.

반면에 자바의 String과 같은 변경될 확률이 매우 낮은 경우 결합도를 신경 쓰지 않는다.

그러나 직접 작성한 코드의 경우 항상 불안정하고 언제라도 변경될 수 있다. 따라서 결합도에 신경 써야 한다.

**응집도와 결합도는 변경과 관련이 깊다**. 변경이 쉬우면 높은 응집도 낮은 결합도를 가졌다고 할 수 있다.

**캡슐화의 정도**가 응집도 결합도에 영향을 미친다. 그렇기 때문에 ***응집도 결합도를 고민하기 전에 캡슐화부터 향상하자.***

---

### **데이터 중심 설계의 문제점**

### **캡슐화 위반**

```arduino
import java.util.Collections;
import java.util.List;

public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;// 할인 조건의 목록private MovieType movieType;// 할인 정책의 종류 결정private Money discountAmount;// 할인 조건 직접 정의private double discountPercent;// 할인 조건 직접 정의public MovieType getMovieType() {
        return movieType;
    }

    public void setMovieType(MovieType movieType) {
        this.movieType = movieType;
    }

    public Money getFee() {
        return fee;
    }

    public void setFee(Money fee) {
        this.fee = fee;
    }

    public List<DiscountCondition> getDiscountConditions() {
        return Collections.unmodifiableList(discountConditions);
    }

    public void setDiscountConditions(List<DiscountCondition> discountConditions) {
        this.discountConditions = discountConditions;
    }

    public Money getDiscountAmount() {
        return discountAmount;
    }

    public void setDiscountAmount(Money discountAmount) {
        this.discountAmount = discountAmount;
    }

    public double getDiscountPercent() {
        return discountPercent;
    }

    public void setDiscountPercent(double discountPercent) {
        this.discountPercent = discountPercent;
    }
}
```

Movie 클래스는 오직 메서드를 통해서만 객체 내부에 접근이 가능하다. 하지만 퍼블릭 인터페이스에서 내부에 어떤 인스턴스 변수가 존재하는지를 드러내고 있어 캡슐화에 어긋난다.

***협력을 고려하지 않고 설계를 하면***

→ 추측에 의한 설계를 하게 된다

→ 그렇게 되면 접근자, 수정자에 과도하게 의존하게 되고

→ 내부 상태를 드러내는 메서드를 최대한 많이 추가하게 된다

→ 내부 구현이 퍼블릭 인터페이스에 그대로 노출되고

→ **캡슐화를 위반**하는 **변경에 취약한 설계**를 하게 된다.

---

### **높은 결합도**

데이터 중심 설계는 접근자, 수정자를 통해 **내부 구현을 인터페이스에 드러나게 하기 때문에** 캡슐화를 위반하고 이는 **클라이언트가 구현에 강하게 결합됨을 의미한다**. 이러면 단지 객체의 내부 구현을 변경했음에도 해당 인터페이스에 의존하는 모든 클라이언트도 변경해야 한다.

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        ...
        Money fee;// 만약 여기를 변경하면 (타입을 변경)if (discountable) {
            ...
// getFee()의 반환타입을 변경해야하고// 변경된 타입에 맞게 클래스의 구현도 수정해야한다.
            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        } else {
            fee = movie.getFee().times(audienceCount);
        }

        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```

또한, 여러 데이터 객체들을 사용하는 **제어 로직**이 **특정 객체 안에 집중되기 때문에** 하나의 제어 객체가 다수의 데이터 객체에 **강하게 결합된**다.

!https://blog.kakaocdn.net/dn/cl84QT/btrqQerVVxW/2S5wZPsqbsj8gNkynm11uk/img.png

이런 식으로 데이터 중심의 설계는 전체 시스템을 하나의 거대한 덩어리로 만들어서 어떤 변경이라도 발생하면 전체를 요동치게 한다.

### **낮은 응집도**

**하나의 모듈 안에** **여러 가지 이유로 변경이 발생**하는 경우 **응집도가 낮다.** 응집도가 낮은 경우 두 가지 단점을 발생시키다.

1) 변경과 아무 상관없는 코드들이 영향을 받는다.

2) 하나의 요구 사항 반영을 위해 여러 모듈을 동시에 수정해야 한다.

> 단일 책임 원칙
> 

---

### **캡슐화를 지키자**

데이터 중심 설계가 낮은 응집도, 높은 결합도를 갖게 되는 근본적인 이유가 바로 "캡슐화를 위반했기 때문"이다.

객체는 스스로의 상태를 책임져야 하며 외부에서는 인터페이스에 정의된 메서드를 통해서만 상태에 접근할 수 있어야 한다.

```java
public class Rectangle {
    private int left;
    private int top;
    private int right;
    private int bottom;

    public Rectangle(int left, int top, int right, int bottom){
        this.left = left;
        this.top = top;
        this.right = right;
        this.bottom = bottom;
    }

    public int getLeft(){
        return left;
    }
    public void setLeft(int left){
        this.left = left;
    }
    public int getTop(){
        return top;
    }
    public void setTop(int top){
        this.top = top;
    }
    public int getRight(){
        return right;
    }
    public void setRight(int right){
        this.right = right;
    }
    public int getBottom(){
        return bottom;
    }
    public void setBottom(int bottom){
        this.bottom = bottom;
    }
}
```

이 클래스는 사각형의 좌표를 포함하고 접근자와 수정자 메서드를 제공한다.이런 경우 만약 사각형의 크기를 늘리거나 줄리는 여러 상황에서 접근자와 수정자를 이용해 값을 수정하는 코드가 중복될 가능성이 크다.또한, 사각형의 좌표를 표현하는 용어를 바꿀 때 변수뿐 아니라 여러 메서드까지 수정해야 하는 '변경에 취약'한 특징을 갖고 있다.

바로 이런 문제를 해결하는 방법이 '캡슐화'다.

```java
class Rectangle{
 public void enlarge(int multiple){
  right *= multiple;
  bottom *= multiple;
 }
}
```

Rectangle이라는 클래스에 크기를 조절하는 메서드를 넣음으로써 객체가 자기 스스로를 책임지도록 '책임의 이동'을 수행했다.

### **스스로 자신의 데이터를 책임지는 객체**

!https://blog.kakaocdn.net/dn/lxhPO/btrrbSJhPSK/jH311yLhSgxsKMMVz4ighK/img.png

DDD 초기 모습

!https://blog.kakaocdn.net/dn/botKBz/btrrb59t9vk/HHIujSAM9KxP8fWEHgStYk/img.png

캡슐화 진행 후

데이터를 처리하는 데 필요한 메서드를 데이터를 가지고 있는 객체 스스로 구현하고 있다. 따라서 이 객체들은 스스로를 책임진다고 말할 수 있다.

### **여전히 부족한 이유**

### **캡슐화 위반**

```java
public class DiscountCondition {
	private DiscountConditionType type;
	private int sequence;
	private DayOfWeek dayOfWeek;
	private LocalTime startTime;
	private LocalTime endTime;

	public DiscountConditionType getType() {// 내부에 DiscountCondition 타입을 갖고 있다는 것을 드러낸다.
		...
	}
	public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime time) {// 속성을 드러낸다.
		...
	}
	public boolean isDidcountable(int sequence) {
		...
	}
}
```

인스턴스 변수로 포함되어있다는 사실을 인터페이스를 통해서 외부에 노출하고 있다.

여기서 클래스의 속성을 변경하는 경우 해당 메서드를 사용하는 모든 클라이언트까지 수정해야 한다.

```java
import java.util.Collections;
import java.util.List;

public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;// 할인 조건의 목록private MovieType movieType;// 할인 정책의 종류 결정private Money discountAmount;// 할인 조건 직접 정의private double discountPercent;// 할인 조건 직접 정의public MovieType getMovieType() {
        return movieType;
    }
    public Money calculateAmountDiscountedFee(){ ... }
    public Money calculatePercentDiscountedFee(){ ... }
    public Money calculateNoneDiscountedFee(){ ... }
}
```

calculateAmountDiscountedFee(), calculatePercentDiscountedFee(), calculateNoneDiscountedFee()는 할인 정책에는 금액 할인, 비율 할인, 미적용 세 가지가 존재한다는 것을 드러내고 있다.

### **높은 결합도**

```java
import java.time.LocalDateTime;
import java.util.Collections;
import java.util.List;

public class Movie {
	public boolean isDiscountable(LocalDateTime whenScreened, int sequence) {
		for(DiscountCondition condition : discountConditions) {
			if(condition.getType() == DiscountConditionType.PERIOD) {
				if(condition.isDiscountable(whenScreened.getDayOfWeek(), whenScreened.toLocalTime())) {
					return true;
				}
			}else {
				if(condition.isDiscountable(sequence)) {
					return true;
				}
			}
			return false;
		}
	}
}
```

1) PERIOD 가 바뀌면 Movie를 수정해야 한다.

2) DiscountCondition 종류가 변경되면, Movie의 조건을 수정해야 한다.

3) DiscountCondition을 만족하는지 판단하는데 필요한 정보가 변경되면, isDiscountable()의 파라미터를 수정해야 하고 메서드 시그니처도 바꿔야 하고 이 메서드에 의존하는 Screening까지 변경해야 한다.

### **낮은 응집도**

위의 3) 상황을 보면 하나의 변경을 하기 위해 코드의 여러 곳을 동시에 변경해야 한다. 이는 응집도가 낮다는 증거이다.

### **데이터 중심 설계의 문제점**

### **1) 객체의 행동보다 상태에 초점을 둔다**

데이터는 고작 구현의 일부라는 것을 명심하자.

데이터 주도 설계는 데이터에 대한 결정을 너무 이른 시간에 한다.첫 번째 설계는 public 속성과 큰 차이가 없는 접근자 수정자를 과도하게 많이 추가했기 때문에 실패했다.두 번째 설계는 데이터에 관한 지식이 객체의 인터페이스에 모두 드러나 실패했다.

### **2) 객체를 고립시킨 채 오퍼레이션을 정의하도록 만든다**

데이터 중심의 설계는 초점을 내부에 둔다. 그러다 보면 이미 구현된 객체의 인터페이스를 억지로 끼워 맞출 수밖에 없다. 결과적으로 올바른 협력을 하지 못하게 된다.

---

# 정리

### **책임 주도 설계의 중요성 강조**

책에서 책임 주도 설계(RDD)의 중요성이 강조되었습니다. 이는 객체의 행동을 정의함에 있어서 객체가 '할 수 있는 것'과 '해야 하는 것'에 초점을 맞추는 것입니다. 책임 주도 설계는 객체가 수행해야 하는 행동을 중심으로 설계를 진행하며, 이는 객체 간의 협력을 자연스럽게 이끌어낼 수 있습니다. 이 점을 강조하는 것은 설계의 품질을 높이는 데 중요한 요소입니다.

### **SOLID 원칙과의 연결**

설계 품질을 논할 때, SOLID 원칙을 언급하지 않을 수 없습니다. SOLID 원칙은 객체지향 설계의 핵심 원칙으로, 응집도를 높이고 결합도를 낮추는 데 기여합니다. 이 원칙들을 당신의 설계에 적용함으로써, 변경에 유연하고 확장 가능한 시스템을 구축할 수 있습니다.

- **단일 책임 원칙(SRP)**
한 클래스는 하나의 책임만 가져야 합니다. 이는 응집도를 높이는 데 직접적인 영향을 줍니다.
- **개방-폐쇄 원칙(OCP)**
소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에는 열려 있어야 하지만, 변경에는 닫혀 있어야 합니다.
- **리스코프 치환 원칙(LSP)**
서브타입은 언제나 그것의 기반 타입으로 교체할 수 있어야 합니다.
- **인터페이스 분리 원칙(ISP)**
클라이언트는 자신이 이용하지 않는 인터페이스에 의존하면 안 됩니다.
- **의존성 역전 원칙(DIP)**
고수준 모듈은 저수준 모듈에 의존해서는 안 되며, 둘 다 추상화에 의존해야 합니다.

### **설계의 유연성과 재사용성**

설계의 유연성과 재사용성은 캡슐화, 응집도, 결합도와 밀접한 관련이 있습니다. 유연하고 재사용 가능한 설계를 위해서는 캡슐화를 강화하고, 응집도를 높이며, 결합도를 낮추어야 합니다. 이는 시스템의 각 부분을 독립적으로 개발하고, 테스트하며, 유지보수하는 데 도움이 됩니다.

### **변경 관리**

설계의 궁극적 목표는 변경을 용이하게 관리하는 것입니다. 당신의 정리에서도 언급되었듯이, 설계는 변경에 대응하기 위해 존재합니다. 효과적인 변경 관리를 위해서는 시스템의 각 부분이 독립적으로 변경될 수 있도록 설계해야 합니다. 이는 캡슐화를 강화하고, 응집도를 높이며, 결합도를 낮추는 것으로 달성될 수 있습니다.

### **결론**

설계 품질과 트레이드오프에 대한 내용은 매우 중요한 개념들을 다루고 있습니다.
보다 유연하고 재사용 가능하며, 변경에 유연하게 대응할 수 있는 설계를 만드는 데 도움이 될 것입니다.
객체지향 설계의 핵심은 적절한 책임의 할당을 통해 높은 응집도와 낮은 결합도를 달성하는 것이며,
 이는 SOLID 원칙과 캡슐화를 통해 실현될 수 있습니다.
