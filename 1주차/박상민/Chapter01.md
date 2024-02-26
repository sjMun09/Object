# 📕 [Object] 1장. 객체, 설계


## 📌 1. 티켓 판매 어플리케이션 구현하기
작은 소극장을 경영하고 있다고 생각해보자.

초기의 코드는 Theater(극장) 객체에서 관객, 관객의 가방, 초대장, 티켓, 티켓 판매원, 티켓 판매소를 알고 있는 상태이다.
그리고 티켓 구매 로직까지 모두 책임지고 있다.

현재의 티켓 판매 어플리케이션은 절차지향적이다. 이를 개선해 나가야 한다.

**구현할 클래스 다이어그램**
![](https://velog.velcdn.com/images/pp8817/post/68468b77-9a2e-468e-91de-343b44887a00/image.png)

- - -
우선 절차 지향 방식의 Theater는 다음과 같다.

```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket(); // 판매원, 관객 객체의 자율성 낮음
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket(); 
            audience.getBag().minusAmount(ticket.getFee());

                        ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```
한 눈에 봐도 절차지향적인 코드이다.

이 작은 프로그램에는 몇 가지 문제를 가지고 있다.

### ✔︎ 무엇이 문제일까?
**로버트 마틴(Robert Martin)**은 소프트웨어 모듈이 가져야 하는 세 가지 기능에 관해 설명한다.

1. 모듈은 정상적으로 실행되어야 한다.
   2.** 변경에 용이해야 한다.**
3. 이해하기 쉬워야 한다.

위 코드는 필요한 기능을 오류 없이 정확하게 수행하고 있다. 제대로 동작해야 한다는 제약 또한 만족한다.

그러나 변경이 용이하고 이해하기 쉬운가?
**아니다.**

**코드의 문제점**
- 예상을 빗나간다.
- 변경에 취약하다.

Theater 클래스의 enter 메서드가 수행하는 일을 말로 풀어보자.
> 소극장은 관람객의 가방을 열어 그 안에 초대장이 들어 있는지 살펴본다. 가방 안에 초대장이 들어 있으면 판매원은 매표소에 보관돼 있는 티켓을 관람객의 가방 안으로 옮긴다. 가방 안에 초대장이 들어 있지 않다면 관람객의 가방에서 티켓 금액만큼의 현금을 꺼내 매표소에 적립한 후에 ㅁ매표소에 보관돼 있는 티켓을 관람객의 가방 안으로 옮긴다.

무엇이 문제일까?
문제는 바로 **관람객과 판매원이 소극장의 통제를 받는 수동적인 존재라는 것이다.**

코드를 어렵게 만드는 또 다른 이유가 있다. 이 코드를 이해하기 위해서는 여러 가지 세부적인 내용들을 한 번에 기억하고 있어야 한다. 앞으로 돌아가 Theater의 enter 메서드를 다시 한 번 살펴보자.

```java
public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket(); // 판매원, 관객 객체의 자율성 낮음
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket(); 
            audience.getBag().minusAmount(ticket.getFee());

                        ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
```
이 메서드를 이해하기 위해서는 Audience가 Bag를 가지고 있고, Bag 안에는 현금과 티켓이 들어 있으며 TicketSeller가 TicekOffice에서 티켓을 판매하고, TicketOffice 안에 돈과 티켓이 보관돼 있다는 모든 사실을 동시에 기억해야 한다.

이 코드는 하나의 클래스 or 메서드에서 너무 많은 세부사항을 다루기 때문에 코드를 작성하는 사람뿐만 아니라 코드르 읽고 이해해야 하는 사람 모두에게 큰 부담을 준다.

**하지만 더 심각한 문제가 있다**
Audience와 TicketSeller를 변경할 경우 Theater도 함께 변경해야 한다는 점이다.

### ✔︎ 변경에 취약한 코드

예를 들어 관람객이 가방을 들고 있다는 가정이 변경된다고 해보자.
Thearter는 관람객이 가방을 들고 있고 판매원이 매표소에서만 티켓을 판매한다는 **지나치게 세부적인 사실에 의존**해서 동작한다.

Audience 클래스에서 Bag을 제거해야 할 뿐만 아니라 Audience의 Bag에 직접 접근하는 Theater의 enter 메서드 역시 수정해야 한다.

이처럼 다른 Class가 Audience의 내부에 대하여 더 많이 알면 알수록 Audience의 변경이 어려워 진다.

이것이 객체 사이의 **의존성(dependency)**와 관련된 문제이다.
의존성이라는 말 내부에는 어떤 객체가 변경될 때 그 객체에게 의존하는 다른 객체도 함께 변경될 수 있다는 사실이 내포되어 있다.

> 그렇다고 해서 객체 사이의 의존성을 완전히 없애는 것이 정답은 아니다. 객체지향 설계는 서로 의존하면서 협력하는 객체들의 공동체를 구축하는 것이다. 따라서 우리의 목표는 **애플리케이션의 기능을 구현하는 데 필요한 최소한의 의존성만 유지하고 불필요한 의존성을 제거하는 것**이다.

객체 사이의 의존성이 과한 경우를 가리켜 **결합도(coupling)**가 높다고 말한다. 반대로 객체들이 합리적인 수준으로 의존할 경우에는 결합도가 낮다고 말한다.

결합도는 의존성과 관련돼 있기 대문에 결합도 역시 변경과 관련이 있다. 객체 사이의 결합도를 낮춰 변경이 용이한 설계를 만드는 것이 중요하다.

## 📌 2. 설계 개선하기
우리는 변경과 의사소통이라는 문제가 서로 엮여 있음에 주목해야 한다.

코드를 이해하기 어려운 이유는 Theater가 관람객의 가방과 판매원의 매표소에 직접적으로 접근하기 때문이다. Theater가 Audience와 TicketSeller에 결합된다는 것을 의미한다.

**과연 Theater가 관람객이 가방을 갖고 있다는 사실과, 판매원이 매표소에서 티켓을 판매한다는 사실을 알아야 한다고 생각하는가?**

전혀 아니다.

관람객이 스스로 요금을 계산하고, 판매원이 스스로 티켓을 판매한다면 모든 문제가 해결된다.
**관람객과 판매원을 자율적인 존재로** 만들어야 하는 것이다.

**1.** Thear의 enter 메서드에서 TicketOffice에 접근하는 모든 코드를 TicketSeller 내부로 숨기자. TicketSeller에 sellTo 메서드를 추가하고 Theater에 있던 로직을 이 메서드로 옮기자.
```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

        // ticketSeller에게 티켓 판매 책임을 위임
    public void enter(Audience audience) {
        ticketSeller.sellTo(audience);
    }
}
```
```java
public class TicketSeller {
        // ...

        // 관객의 구매 책임은 Audience 에게 위임, 판매소 재산 관리 책임은 판매소에게 위임
    public void sellTo(Audience audience) {
        ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}
```
이제 TicketOffice에 대한 접근은 오직 TicketSeller 에서만 가능하다.
이를 **캡슐화**라고 부른다. **캡슐화를 통해 변경하기 쉬운 객체를 만들게 된다.**

이제 Theater의 enter 메서드는 sellTo 메서드를 호출하는 간단한 코드로 바뀐다.

Theater는 오직 TicketSeller의 인터페이스에만 의존한다. TicketSeller가 내부에 TickerOffice 인스턴스를 포함하고 있다는 사실은 **구현**의 영역에 속한다.

**1.** Audience를 캡슐화 하자. TicketSeller는 Audience는 getBag 메서드를 호출해서 Audience 내부의 Bag 인스턴스에 직접 접근한다.

캡슐화 하는 방식은 이전과 같다.
```java
public class Audience {
        // ...

    public Long buy(Ticket ticket) {
        if (bag.hasInvitation()) {
            bag.setTicket(ticket);
            return 0L;
        } else {
            bag.setTicket(ticket);
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}
```
변경 된 코드에서는 Audience가 자신의 가방안에 초대장이 들어있는지 **스스로** 확인한다.
Audience가 가방을 직접 다루기 땜문에 외부에 더이상 getter를 노출시킬 필요가 없다.
getBag()을 제거하고 결과적으로 Bag의 존재를 내부로 캡슐화 할 수 있게 되었다.

**Audience와 TicketSeller를 자율적인 객체로 만들었다.**
![](https://velog.velcdn.com/images/pp8817/post/df8e03e4-a6a9-4cf7-9fa0-18412d0b25aa/image.png)

## ⭐️ 캡슐화와 응집도
핵심은 객체 내부의 상태를 캡슐화하고 객체간에 **오직 메세지를 통해서만 상호 작용**하도록 만드는 것이다.
밀접하게 연관된 작업만을 수행하고 **연관성 없는 작업은 다른 객체에게 위임**하는 객체를 가리켜 응집도가 높다고 말한다.

객체의 응집도를 높이기 위해서는 객체 스스로 자신의 데이터를 책임져야 한다.

이처럼 데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍하는 방식을 객체지향 프로그래밍이라고 부른다.
두 방식 사이에 근본적인 차이를 만드는 것은 **책임(기능)의 이동**이다.

리팩토링 이전에는 작업 흐름이 주로 **Theater에 의해 제어**된다는 사실을 알 수 있다.

> **디미터 법칙**
다른 객체가 어떠한 자료를 갖고 있는지 속사정을 몰라야 한다. (여러 개의 . 도트를 사용하지 말라)
객체 지향 프로그래밍에서 가장 중요한 것은 "**객체가 어떤 데이터를 가지고 있는가?"**가 아니라, **"객체가 어떤 메세지를 주고 받는가?" **이다.</br>
출처: https://mangkyu.tistory.com/147

- - -
**출처**</br>
[오브젝트 - 코드로 이해하는 객체지향 설계](https://smartstore.naver.com/aladinstores/products/7815204109?NaPm=ct%3Dlsvyemdc%7Cci%3Da772aefc1b71a76e7f3412247dc9108aec75e0f6%7Ctr%3Dboksl%7Csn%3D4399901%7Chk%3D0c624b3c7c58fc3c0cc29d777797044a631fb294)
https://github.com/eternity-oop/object