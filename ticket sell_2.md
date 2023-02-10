# ticket sell_2

객체간의 자율성이 부족해보이고 결합도와 의존성이 너무 높다. 개선해보자.



설계를 변경하기 어려운 이유는 Theater가 Audience 와 TicketSeller 뿐만 아니라 Audience 소유의 Bag와 TicketSeller 가 근무하는 TicketOffice 까지 마음대로 접근할 수 있기 때문이다. 해결 방법은 Audience 와 TIcketSeller 가 직접 Bag 과 TicketOffice 를 처리하는 자율적인 존재가 되도록 설계를 변경하는 것이다.

```java
public class TicketSeller {
    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }
    
	public void sellTo(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

이제 Theater 의 enter 메서드를 sellTo 메서드를 호출하는 간단한 코드로 바꾼다.

```java
public class Theater {
    private TicketSeller ticketSeller;
    
    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }
    
    public void Enter(Audience audience) {
		ticketSeller.sellTo(audience);
    }
}
```

수정된 Theater 클래스 어디서도 ticketOffice 에 접근하지 않는다는 사실에 주목하자. Theater 는 ticketOffice 가 TicketSeller 내부에 존재한다는 사실을 알지 못한다. Theater는 단지 ticketSeller 가 sellTo 메시지를 응답할 수 있다는 사실만 알고 있을 뿐이다.



TicketSeller 다음으로 Audience의 캡슐화를 개선하자. TicketSeller 는 Audience 의 getBag 메서드를 호출해서 Audience 내부의 Bag 인스턴스에 직접 접근하자. Bag 인스턴스에 접근하는 객체가 Theater 에서 TicketSeller 로 바뀌었을 뿐 Audience 는 여전히 자율적인 존재가 아닌 것이다.

TicketSeller 와 동일한 방법으로 Audience 의 캡슐화를 개선할 수 있다. Bag 에 접근하는 모든 로직을 Audience 내부로 감추기 위해 Audience 에 buy 메서드를 추가하고 TicketSeller 의 sellTo 메서드에서 getBag 메서드에 접근하는 부분을 buy로 옮기자.

```java
public class Audience {
    private Bag bag;
    
    public Audience(Bag bag) {
        this.bag = bag;
    }
    
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

변경된 코드에서 Audience 는 자신의 가방 안에 초대장이 있는지 스스로 확인한다. 외부의 제3자가 자신의 가방을 열어보도록 허용하지 않는다. Audience 가 Bag을 직접 처리하기 때문에 외부에서는 더 이상 Audience 가 Bag 을 소유하고 있다는 사실을 알 필요가 없다.

```java
public class TicketSeller {
    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }
    
	public void sellTo(Audience audience) {
		ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}
```



# 더 개선할 수 있다.



```java
public class Audience {
    private Bag bag;
    
    public Audience(Bag bag) {
        this.bag = bag;
    }
    
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

분명 audience는 자율적인 존재다. Audience 는 스스로 티켓을 구매하고 가방 안의 내용물을 직접 관리한다. 하지만 Bag은 어떤가? Bag는 과거의 Audience 처럼 스스로 자기 자신을 책임지지 않고 Audience 에 의해 끌려다니는 수동적인 존재다. 이전 예제를 통해 우리가 객체지향에 눈을 떴다면 Bag에도 문제가 있다는 사실을 이해할 수 있을 것이다.

Bag 을 자율적인 존재로 바꿔보자. 방법은 이전과 동일하다. Bag 의 내부 상태에 접근하는 모든 로직을 Bag 안으로 캡슐화해서 결합도를 낮추면 된다. Bag에 Hold 메서드를 추가하자.



```java
public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;
    
    public Long hold(Ticket ticket) {
        if (hasInvitation()) {
            setTicket(ticket);
            return 0L;
        } else {
            setTicket(ticket);
            minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
    
    private boolean hasInvitation() {
        return invitation != null;
    }
    
    private void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }
    
    private void minusAmount(Long amount) {
        this.amount -= amount;
    }
}
```

Bag 의 구현을 캡슐화시켰으니 이제 Audience 를 Bag 의 구현이 아닌 인터페이스에만 의존하도록 수정하자.

```java
public class Audience {
    public Long buy(Ticket ticket) {
		return bag.hold(ticket);
    }
}
```



TicketSeller 역시 TicketOffice 의 자율권을 침해한다. 현재의 TicketSeller 는 TicketOffice 에 있는 Ticket 을 마음대로 꺼내서는 자기 멋대로 Audience 에게 팔고 Audience 에게 받은 돈을 마음대로 TicketOffice 에 넣어버린다.



```java
public class TicketSeller {
    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }
    
	public void sellTo(Audience audience) {
		ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}
```

잃어버린 TicketOffice 의 자율권을 찾아주자. TicketOffice 에 sellTicketTo 메서드를 추가하고 TicketSeller 의 sellTo 메서드의 내부코드를 이 메서드로 옮기자. 아제 getTicket 메서드와 plusAmount 메서드는 TicketOffice 내부에서만 사용되기 때문에 가시성을 public을 private으로 변경할 수 있다.



```java
public class TicketOffice {
	public void sellTicketTo(Audience audience) {
        plusAmount(audience.buy(getTicket()))
    }

    private Ticket getTicket() {
        return tickets.remove(0);
    }
    
    private void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

```java
public class TicketSeller {
    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }
    
	public void sellTo(Audience audience) {
		ticketOffice.sellTicketTo(audience);
    }
}
```

만족스러운가? 안타깝게도 이 변경은 처음에 생각했던 것만큼 만족스럽지 않다. 그 이유는 TicketOffice와 Audience 사이에 의존성이 추가됐기 때문이다. 



현재로서의 Audience 에 대한 결합도와 TicketOffice 의 자율성 모두를 만족시키는 방법이 잘 떠오르지 않는다. 트레이드오프의 시점이 왔다.  설계는 균형의 예술이다. 훌륭한 설계는 적절한 트레이드오프의 결과물이라는 사실을 명심하라. 이러한 트레이드오프 과정이 설계를 어려우면서도 흥미진진한 작업으로 만드는 것이다.