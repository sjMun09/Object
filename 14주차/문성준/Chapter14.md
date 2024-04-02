# ch14 일관성 있는 협력

---

---

## 서론

객체는 협력을 위해 존재한다.

협력은 객체가 존재하는 이유와 문맥을 제공한다.

객체지향 패러다임의 큰 장점은 설계를 재사용할 수 있다는 것이다.
하지만 재사용은 공짜로 얻어지지 않는다. 재사용을 위해서는 객체들의 협력 방식을 일관성 있게 만들어야 한다.

가능하면 유사한 기능을 구현하기 위해 유사한 협력 패턴을 사용하라.

---

## 구간별 방식 구현하기

구현의 가장 큰 문제점은 설계에 일관성이 없다는 것이다.
개념적으로는 연관돼 있지만 구현 방식에 있어서는 완전히 제각각이라는 것이다.

일관적인 설계가 아닐 경우 나타나는 문제점(비일관성)

1. 새로운 구현을 추가해야 하는 상황 (=기존의 구현을 이해해야 하는 상황)
요구사항의 관점에서는 여러 개의 규칙을 사용한다는 공통점을 공유하지만 구현 방식은 완전히 다르다.
세 가지 기본 정책에 대한 세 가지 다른 구현 방식이 존재하는 것이다.
2. 코드를 이해하기 어렵다.

결론은 유사한 기능을 서로 다른 방식으로 구현해서는 안 된다는 것이다.
유사한 기능은 유사한 방식으로 구현해야 한다.

규칙을 정의하는 새로운 클래스를 추가하기로 결정했다면, 클래스의 메서드 안에서 부모 클래스의 메서드를 호출하는 부분을 눈여겨 봐야한다.

### **기본 클래스 및 인터페이스 정의**

먼저, 여러 규칙을 정의할 수 있는 인터페이스를 생성합니다. 이를 **`Rule`**이라고 합시다. 각 규칙은 **`applyRule`** 메서드를 통해 특정 작업을 수행할 수 있습니다.

```java
interface Rule {
    void applyRule();
}
```

### **규칙 구현**

다음으로, **`Rule`** 인터페이스를 구현하는 세 가지 구체적인 규칙 클래스를 정의합니다. 이 예에서는 각 클래스가 **`applyRule`** 메서드를 다르게 구현하되, 같은 목적을 위해 사용되는 것을 상상할 수 있습니다. 그러나 실제 구현에서는 이러한 규칙들이 유사한 방식으로 작동하도록 노력해야 합니다.

```java
class RuleA implements Rule {
    public void applyRule() {
        System.out.println("Rule A applied.");
    }
}

class RuleB implements Rule {
    public void applyRule() {
        System.out.println("Rule B applied.");
    }
}

class RuleC implements Rule {
    public void applyRule() {
        System.out.println("Rule C applied.");
    }
}
```

### **규칙 관리 클래스**

일관성을 유지하려면, 규칙을 적용하는 방식도 일관성 있게 관리해야 합니다. 규칙을 적용하는 클래스를 만들어서, 이 클래스가 여러 규칙 중 하나를 선택하여 적용할 수 있도록 합니다. 이 클래스는 또한 부모 클래스에서 제공하는 기능을 활용할 수 있어야 합니다.

```java
class RuleManager {
    private Rule rule;

    public RuleManager(Rule rule) {
        this.rule = rule;
    }

    public void execute() {
        rule.applyRule();
    }
}
```

### **실행 예제**

마지막으로, 각 규칙을 적용하는 방법을 보여주는 실행 예제를 만듭니다.

```java
class Main {
    public static void main(String[] args) {
        Rule ruleA = new RuleA();
        RuleManager managerA = new RuleManager(ruleA);
        managerA.execute();

        Rule ruleB = new RuleB();
        RuleManager managerB = new RuleManager(ruleB);
        managerB.execute();

        Rule ruleC = new RuleC();
        RuleManager managerC = new RuleManager(ruleC);
        managerC.execute();
    }
}
```

이 예제는 유사한 기능(여기서는 규칙 적용)을 서로 다른 방식으로 구현하는 대신, 일관성 있는 방식으로 규칙을 적용하는 방법을 보여줍니다. 모든 규칙이 **`Rule`** 인터페이스를 구현하므로, **`RuleManager`** 클래스는 어떤 규칙이든지 동일한 방식으로 처리할 수 있습니다. 이는 코드의 유지 관리를 용이하게 하고, 새로운 규칙을 추가하는 것을 단순화합니다.

즉, 각 클래스는 하나의 책임만을 수행한다는 것을 코드를 통해 확인할 수 있었다.

구현에 문제가 있다는 사실이 잘 이해가 되지 않는 사람들도 있을 것이다.
지금까지 구현한 코드와 일관성 있게 작성한 코드를 비교해 보는 것이다.
→ 사실 이 부분은 나도 이해가 되질 않는다. 무슨 말인지 언젠가 이해하는 날이 오겠지..? (488page)

> **코드 재사용을 위한 상속은 해롭다.**
10장을 주의 깊게 읽었다면 상속을 잘못 사용한 경우의 코드인 것을 알 수 있다(책의 교재).
필자의 코드엔 문제가 없다. 코드의 재사용 목적을 위해 상속을 사용한 것이 아니라,
상속보다는 인터페이스와 구성을 사용하여 다형성과 코드 재사용성을 달성하는 방식을 보여주고 있기 떄문입니다.
> 

---

# 설계에 일관성 부여하기

일관성 있는 설계를 만드는 데 가장 훌륭한 조언은 1) **다양한 설계 경험**을 익히라는 것이다.
설계 경험이 풍부하면 풍부할수록 어떤 위치에서 일관성을 보장해야 하고 일관성을 제공하기 위해 어떤 방법을 사용해야 하는지를 직관적으로 결정할 수 있기 떄문입니다.

2) **널리 알려진 디자인 패턴을 학습하고 변경이라는 문맥 안에서 디자인 패턴을 적용해보자.**
디자인 패턴은 특정한 변경에 대해 일관성 있는 설계를 만들 수 있는 경험 법칙을 모아놓은 일종의 설계 템플릿이다. 디자인 패턴을 학습하면 빠른 시간 안에 전문가의 경험을 흡수할 수 있어서 상당히 좋은 방법입니다.

> **즉, 디자인 패턴을 꼭 학습해야 합니다.**
> 

협력을 일관성 있게 만들기 위해 다음과 같은 기본 지침을 따르는 것이 도움이 됩니다.

1. 변하는 개념을 변하지 않는 개념으로부터 분리하라
2. 변하는 개념을 캡슐화하라

두 가지 지침은 훌륭한 구조를 설계하기 위해 따라야 하는 기본 원칙입니다.
모든 원칙과 개념들 역시 대부분 변경의 캡슐화라는 목표를 향합니다.

> app에서 달라지는 부분을 찾아내고, 달라지지 않는 부분으로부터 분리시킨다. 여러 설계 원칙 중에서 첫 번째 원칙이다.
→ ”바뀌는 부분을 따로 뽑아서 캡슐화한다. 그렇게 하면 나중에 바뀌지 않는 부분에는 영향을 미치지 않은 채로 그 부분만 고치거나 확장할 수 있따”
> 

## 조건 로직 대 객체 탐색

하나는 할인 종류를 결정하는 부분이고 다른 하나는 할인 정책을 결정하는 부분이다.
나쁜 이유는 변경의 주기가 서로 다른 코드가 한 클래스 안에 뭉쳐있기 때문이다.

> ***객체지향에서 변경을 다루는 전통적인 방법은 조건 로직을 객체 사이의 이동으로 바꾸는 것이다.***
> 

이를 이해하기 쉽게 자바 코드로 알아보자.

### **예제: 결제 수단에 따른 처리**

다음은 조건 로직을 사용하여 결제를 처리하는 코드의 예시입니다. 
이 예시에서는 결제 수단에 따라 다른 로직을 실행합니다.

```java
public class PaymentProcessor {
    public void processPayment(String paymentType, double amount) {
        if ("credit".equals(paymentType)) {
            System.out.println("Processing credit payment of " + amount);
            // 신용카드 처리 로직
        } else if ("debit".equals(paymentType)) {
            System.out.println("Processing debit payment of " + amount);
            // 직불카드 처리 로직
        } else if ("paypal".equals(paymentType)) {
            System.out.println("Processing PayPal payment of " + amount);
            // PayPal 처리 로직
        }
        // 여러 다른 조건들...
    }
}
```

이 코드는 작동하지만, 새로운 결제 수단이 추가될 때마다 **`processPayment`** 메서드에 조건문을 추가해야 합니다. 이는 코드의 복잡성을 증가시키고, 유지 보수를 어렵게 만듭니다.

### **다형성을 사용한 리팩토링**

이제 조건 로직을 객체 사이의 이동으로 바꾸어, 다형성을 활용한 리팩토링을 적용해 보겠습니다.

먼저, 결제를 처리하는 인터페이스를 정의합니다.

```java
interface PaymentMethod {
    void processPayment(double amount);
}
```

그 다음, **`PaymentMethod`** 인터페이스를 구현하는 각 결제 방식에 대한 클래스를 생성합니다.

```java
class CreditPayment implements PaymentMethod {
    public void processPayment(double amount) {
        System.out.println("Processing credit payment of " + amount);
        // 신용카드 처리 로직
    }
}

class DebitPayment implements PaymentMethod {
    public void processPayment(double amount) {
        System.out.println("Processing debit payment of " + amount);
        // 직불카드 처리 로직
    }
}

class PayPalPayment implements PaymentMethod {
    public void processPayment(double amount) {
        System.out.println("Processing PayPal payment of " + amount);
        // PayPal 처리 로직
    }
}
```

마지막으로, 결제 처리기를 다형성을 사용하여 리팩토링합니다.

```java
public class PaymentProcessor {
    private PaymentMethod paymentMethod;

    public PaymentProcessor(PaymentMethod paymentMethod) {
        this.paymentMethod = paymentMethod;
    }

    public void processPayment(double amount) {
        paymentMethod.processPayment(amount);
    }
}
```

이제 결제를 처리하는 방법을 보겠습니다.

```java
public class Main {
    public static void main(String[] args) {
        PaymentProcessor creditPaymentProcessor = new PaymentProcessor(new CreditPayment());
        creditPaymentProcessor.processPayment(100.0);

        PaymentProcessor debitPaymentProcessor = new PaymentProcessor(new DebitPayment());
        debitPaymentProcessor.processPayment(50.0);

        PaymentProcessor paypalPaymentProcessor = new PaymentProcessor(new PayPalPayment());
        paypalPaymentProcessor.processPayment(75.0);
    }
}
```

이 리팩토링을 통해, 새로운 결제 수단이 추가될 때 기존 코드를 변경하지 않고도 새로운 클래스를 추가하기만 하면 됩니다. 이는 "개방-폐쇄 원칙"(OCP)을 따르는 것으로, 기존 코드는 수정하지 않으면서 기능 확장을 할 수 있게 합니다.

즉, 다형성은 객체 사이의 이동으로 바꾸기 위해 객체지향이 제공하는 설계 기법이라는 것이다.

![IMG_4312.heic](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/b61d92e9-578f-4d0a-af12-912a1d506d68/IMG_4312.heic)

하지만 실제로 협력에 참여하는 주체는 구체적인 객체다.

![IMG_4313.heic](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/d8d9198d-8f9a-461d-ad69-417baa4eb3f4/IMG_4313.heic)

현재의 할인 정책이 어떤 종류인지 판단하지 않는다.
단지 DiscountPolicy로 향하는 참조를 통해 메시지를 전달할 뿐이다.

실행할 메서드를 결정하는 것은 순전히 메시지를 수신한 객체의 책임이다.
DiscountCondition으로 향하는 참조를 통해 메시지를 전달할 뿐이다.

클래스를 분리하기 위해 어떤 기준을 따르는 것이 좋을까 ?

→ 변경의 이유와 주기다.

클래스는 명확히 단 하나의 이유에 의해서만 변경돼야 하고 클래스 안의 모든 코드는 함께 변경돼야하기 때문이다.

즉, 단일 책임 원칙을 따르도록 클래스를 분리해야 한다는 것이다.

유사한 행동을 수행하는 작은 클래스들이 자연스럽게 역할이라는 추상화로 묶이게 되고 역할 사이에서 이뤄지는 협력 방식이 전체 설계의 일관성을 유지할 수 있게 이끌어주기 때문이다.

일관성 있게 만들기 위해 따라야 하는 

첫 번째 지침

> 변하는 개념을 변하지 않는 개념으로부터 분리하라
> 

역할을 추상 크랠스와 인터페이스로 구현하고, 변하는 개념을 별도의 서브타입으로 분리한 후 이 서브타입들을 클라이언트로부터 캡슐화하라는 것이다.

두 번째 지침

> 변하는 개념을 캡슐화하라.
> 

핵심은 훌륭한 추상화를 찾아 추상화에 의존하도록 만드는 것이다.
추상화에 대한 의존은 결합도를 낮추고 결과적으로 대체 가능한 역할로 구성된 협력을 설계할 수 있게 해준다.
따라서 선택하는 추상화의 품질이 캡슐화의 품질을 결정한다.

> 타입을 캡슐화하고 낮은 의존성을 유지하기 위해서는 지금까지 살펴본 다양한 기법들이 필요하다
챕터 6에서 살펴본 인터페이스 설계 원칙들을 적용하면 구현을 효과적으로 캡슐화하는 코드를 구현할 수 있다.
챕터 8,9장에서 설명한 의존성 관리 기법은 타입을 캡슐화하기 위해 낮은 결합도를 유지할 수 있는방법을 보여준다. 타입을 캡슐화하기 위해서가 아니라 코드 재사용을 위해 상속을 사용하고 있다면 10장을 주의깊게 살펴보자.
상속 대신 11장에서 설명한 합성을 사용하는 것도 캡슐화를 보장하는 훌륭한 방법이며, 13장에서 설명한 원칙을 따르면 LSP준수하는 타입 계층을 구현하는 데 상속을 이용할 수 있을 것이다.
> 

---

## 데이터 은닉

데이터 은닉은 객체 지향 프로그래밍의 중요한 원칙 중 하나로, 클래스의 내부 상태를 외부에서 직접 접근할 수 없도록 제한하여, 객체의 상세 구현을 숨기는 기법입니다. 이 원칙은 프로그램의 보안을 강화하고, 오류 가능성을 줄이며, 유지보수를 용이하게 합니다. 데이터 은닉은 주로 접근 제어자를 사용하여 구현됩니다. 

자바에서는 **`private`**, **`protected`**, **`public`**과 같은 접근 제어자를 통해 데이터 은닉을 구현할 수 있습니다.

- **`private`**: 이 키워드를 사용하면 멤버(변수, 메서드)는 해당 클래스 내부에서만 접근 가능합니다.
- **`protected`**: 이 키워드를 사용하면 멤버는 동일 패키지 내의 다른 클래스 또는 해당 클래스를 상속받는 자식 클래스에서 접근할 수 있습니다.
- **`public`**: 이 키워드를 사용하면 멤버는 어디서든 접근할 수 있습니다.

### **데이터 은닉 예제**

아래의 자바 코드는 데이터 은닉을 구현하는 간단한 예제를 보여줍니다. 

여기서는 **`Person`** 클래스가 있으며, 이 클래스는 **`name`**과 **`age`**라는 두 개의 private 필드를 가집니다. 

이 필드들은 직접적으로 외부에서 접근할 수 없으며, **`getName()`**, **`setName()`**, **`getAge()`**, **`setAge()`** 같은 public 메서드를 통해서만 접근할 수 있습니다.

```java
public class Person {
    private String name; // 외부에서 직접 접근 불가
    private int age; // 외부에서 직접 접근 불가

    // name 필드에 대한 getter 메서드
    public String getName() {
        return name;
    }

    // name 필드에 대한 setter 메서드
    public void setName(String name) {
        this.name = name;
    }

    // age 필드에 대한 getter 메서드
    public int getAge() {
        return age;
    }

    // age 필드에 대한 setter 메서드
    public void setAge(int age) {
        this.age = age;
    }
}
```

이러한 방식으로, **`Person`** 클래스의 **`name`**과 **`age`** 필드는 외부에서 직접 접근하여 변경할 수 없으며, 클래스가 제공하는 메서드를 통해서만 이 필드들의 값을 읽거나 수정할 수 있습니다. 이는 데이터의 무결성을 보장하고, 예상치 못한 오류로부터 프로그램을 보호하는 데 중요한 역할을 합니다.

### **사용 예**

```java
public class Main {
    public static void main(String[] args) {
        Person person = new Person();
        person.setName("John Doe");
        person.setAge(30);

        System.out.println("Name: " + person.getName() + ", Age: " + person.getAge());
    }
}
```

이 예제에서 **`Person`** 객체의 **`name`**과 **`age`** 필드에 접근하기 위해 **`setName()`**, **`setAge()`**, **`getName()`**, **`getAge()`** 메서드를 사용했습니다. 이는 데이터 은닉 원칙을 잘 보여주는 예시로, 클래스의 내부 구현을 캡슐화하고, 외부에서는 오직 제공된 인터페이스(여기서는 메서드)를 통해서만 상호작용할 수 있도록 합니다.

---

## 다양한 종류의 캡슐화가 공존한다.

### **1. 데이터 캡슐화**

데이터 캡슐화는 클래스 내부의 필드(데이터)를 외부에서 직접 접근할 수 없도록 숨기고, 
대신에 이 필드들을 접근하기 위한 메서드(getter&&setter)를 제공하는 것을 의미합니다.

 이를 통해 데이터의 무결성을 보장하고, 클래스의 사용 방식을 명확히 할 수 있습니다.

```java
public class Account {
    private double balance; // 데이터 캡슐화

    public double getBalance() { // 게터 메서드
        return balance;
    }

    public void deposit(double amount) { // 세터 메서드
        if (amount > 0) {
            balance += amount;
        }
    }
}
```

### **2. 메서드 캡슐화**

메서드 캡슐화는 복잡한 로직을 메서드 내부에 숨기고, 단순한 인터페이스를 외부에 제공하는 것입니다.

 이를 통해 코드의 재사용성과 유지보수성이 향상됩니다.

```java
public class Calculator {
    // 메서드 캡슐화
    public int add(int x, int y) {
        return x + y; // 단순화된 인터페이스 제공
    }

    public int multiply(int x, int y) {
        return x * y; // 복잡한 계산 로직 숨김
    }
}
```

### **3. 객체 캡슐화**

객체 캡슐화는 객체의 상태(데이터)와 행동(메서드)을 함께 묶어서, 객체가 독립적인 단위로 작동할 수 있도록 하는 것입니다. 객체 캡슐화는 데이터와 메서드 캡슐화를 포함하며, 객체 간의 상호작용을 메시지 전달을 통해 수행합니다.

```java
public class User {
    private String name; // 데이터 캡슐화
    private List<Account> accounts = new ArrayList<>(); // 객체 캡슐화

    public void addAccount(Account account) {
        accounts.add(account);
    }

    // 메서드 캡슐화를 통해 계좌 잔액 확인
    public double getTotalBalance() {
        double total = 0;
        for (Account account : accounts) {
            total += account.getBalance();
        }
        return total;
    }
}
```

### **4. 서브타입 캡슐화**

서브타입 캡슐화는 상속 또는 인터페이스를 사용하여 다형성을 구현함으로써, 서로 다른 타입의 객체를 동일한 방식으로 처리할 수 있게 하는 것입니다. 이를 통해 코드의 유연성이 향상됩니다.

```java
interface Shape {
    double area();
}

class Rectangle implements Shape {
    private double width;
    private double height;

    // 생성자, 게터, 세터 생략

    @Override
    public double area() { // 메서드 캡슐화
        return width * height;
    }
}

class Circle implements Shape {
    private double radius;

    // 생성자, 게터, 세터 생략

    @Override
    public double area() { // 메서드 캡슐화
        return Math.PI * radius * radius;
    }
}

public class AreaCalculator {
    public double calculateTotalArea(List<Shape> shapes) {
        double totalArea = 0;
        for (Shape shape : shapes) {
            totalArea += shape.area(); // 서브타입 캡슐화
        }
        return totalArea;
    }
}
```

이 예제에서 **`Shape`** 인터페이스는 **`area`** 메서드를 통해 다양한 형태의 도형(서브타입)의 면적을 계산하는 방법을 캡슐화합니다. **`AreaCalculator`**는 **`Shape`** 타입의 객체 리스트를 받아 전체 면적을 계산하는데, 이 과정에서 서브타입 캡슐화를 통해 코드의 유연성을 보여줍니다.

다시 말해, 캡슐화란 단지 데이터 은닉을 의미하는 것이 아닙니다.

코드 수정으로 인한 파급효과를 제어할 수 있는 모든 기법이 캡슐화의 일종입니다.
일반적으로 데이터 캡슐화와 메서드 캡슐화는 개별 객체에 대한 변경을 관리하기 위해 사용하고 객체 캡슐화와 서브타입 캡슐화는 협력에 참여하는 객체들의 관계에 대한 변경을 관리하기 위해 사용한다.

협력을 일관성 있게 만드는 것은 서브 타입 캡슐화와 객체 캡슐화를 조합하는 것이다.
서브 타입 캡슐화와 객체 캡슐화를 적용하는 방법

1. 변하는 부분을 분리해서 타입 계층을 만든다
2. 변하지 않는 부분의 일부로 타입 계층을 합성한다.

## 변경 캡슐화하기

변하는 부분의 공통점을 추상화하는 것도 잊어서는 안 된다.
이제 변하지 않는 부분이 오직 이 추상화에만 의존하도록 관계를 제한하면 변경을 캡슐화할 수 있게 된다.

## 패턴을 찾아라

다시 말하겠다.

**일관성 있는 협력의 핵심은 변경을 분리하고 캡슐화하는 것이다.**
