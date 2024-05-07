## 적용된 객체지향 이론 규칙

- 구현보다는 **추상화 또는 인터페이스에 의존하라**
- 상속보다는 **구성(합성 or 위임); Composition** 활용해라 (”A는 B이다(is-A)”보다 “A에는 B가 있다”가 더 좋을 수 있다.)
- 협력하는 객체 간의 결합도 느슨한 결합을 사용해야 한다.

## Observer 패턴이란?

한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다(1:N) 의존성을 정의한다.

옵저버 패턴에는 구독자와 발행자, 2명의 구성원이 있다. 구독자들은 발행자를 관찰하고 발행자에 변경사항이 발생하면 발행자가 구독자들에게 변경된 사항을 통지하고 구독자들은 통지를 받아 조치를 취하는 행동 패턴을 말한다. 

### 구조

**Subject || Observable || Publisher ; 발행자**

⇒객체의 상태 변경 여부 || 상태 변경에 대한 메시지를 발행하는 주체

**Observer || Subscriber ; 구독자**

⇒ 객체의 상태 변경에 대한 자세한 정보를 전달 받는 구독자 || 상태 변경에 대한 정보가 담겨 있는 메시지를 구독

### 작동원리

**취소된 티켓 발생 알림 서비스**

- 티켓의 상태 변경(예약 → 취소)이 발생 했을 때 취소된 티켓에 대한 정보를 발행하는 CanceledTicketSubject 객체가 있다.
- CanceledTicketNotification 객체는 CanceledTicketSubject 객체의 옵저버(or 구독자)로 등록되어 발행하는 메시지를 받고 싶다. 그래서 CanceledTicketNotification 객체는 CanceledTicketSubject 객체에게 구독 등록 요청을 보낸다. (registerObserver)
- 이제 CanceledTicketSubject 객체가 발행하는 메시지를 CanceledTicketNotification 객체가 받아 볼 수 있게 되었다.티켓이 취소되는 이벤트가 발생하면! 메시지를 발생합니다.(update) 전달 받은 발행된 메시지를 이욯새서 알림 서비스를 등록한 고객의 SNS로 취소된 티켓 정보를 발송할 수 있게 되었다.
- 그런데 이제는 원하는 고객의 수가 많이 사라져 알림 서비스를 종료하려고 한다. 이럴 때는 구독 취소를 요청하면 된다.(removeObserver)

![image](https://github.com/jeongye01/TIL/assets/74299317/3daf7b06-8f70-4d5a-beb7-ee6510bc686c)


### 패턴 적용해보기

**티켓 취소 구독 서비스 구현 - 순수 Observer 패턴**

- IObservable || ISubject || IPublisher

```java
public interface ISubject {
  public boolean register(IObserver newObserver);
  public boolean remove(IObserver observer);
  public boolean sendMessage(Message msg); // notify() -> 이벤트 발생을 알리는 역할
}
```

- ConcreateSubject

```java
@Component
@RequiredArgsConstructor
public class CancelNotification implements ISubject{
      private final Set<IObserver> observers; // 중복 방지
      
      @Override
      public boolean register(IObserver newObserver){
          return observers.add(newObserver);
      }
      
      @Override
      public boolean remove(IObserver observer){
          return observers.remove(observer);
      }
      
      @Override
      public boolean sendMessage(Message msg){
          return observers.stream()
              .filter(observer -> observer.updage(msg))
              .count() == observers.size();
      }
}
```

- IObserver || Subscriber

```java
public interface Observer<T> {
    public boolean update(T message);
}
```

- ConcreateObserver

```java
@Component
@RequiredArgsConstructor
public class TicketCancelObserver implements Observer<ReservationInfo> {
    public final TicketOffice office;
    
    @Override
    public boolean update(ReservationInfo message) throws MessagingException {
        List<UserInfo> userList = office.getSubscribers(message.getPerformanceId());
        userList.stream.map(userInfo ->  {
        emailSender.sendEmail("customer@emailAddress.com","취소된 티켓 정보 알려드립니다.");
        }
        return true;
    }}
}
```

**티켓 취소 구독 서비스 구현 - ApplicationEventPublisher 사용해 보기**

1. what is **ApplicationEventPublisher ?**
    
    ApplicationContext가 상속하는 인터페이스 중 하나이며, 옵저버 패턴의 구현체로 이벤트 프로그래밍에 필요한 기능을 제공한다. 
    
2. 구현방법
    1. Publisher(Subject,Observable)
    
    ```java
    @Transactional
    public void ticketCancelBy(ReservationInfo cancelMessage){
      Reservation reservation = getReservationInfo(cancelMessage);
      reservation.softDelete();
      applicationEventPublisher.publishEvent(cancelMessage);
    }
    ```
    
    b. Subscriber(Observer, Listener) - sync
    
    ` @EventListener ` 를 사용할 경우, 이벤트를 발행하는 시점에 바로 Listener(Observer)에게 이벤트 발생의 알림과 메시지가 전달된다. 그렇기 때문에 티켓 취소 로직에서 예외가 발생하더라도 옵저버에게 이벤트 메시지를 전달하게 되는 케이스가 발생할 수 있다.(커밋이 되기 전에 이벤트가 실행이 됌)
    
    ```java
    @Component
    @Slf4j
    public class TicketCancelNotificationV2 {
       @EventListener
       publci void onTicketCancelHandler(ReservationInfo cancelMessage){
          log.info("Event Message => {}",cancelMessage);
       }
    }
    ```
    
    c. Subscriber(Observer) - async
    
    그래서 @TransactionalEventListener 으로 Observer(Listener,Subscriber)를 등록하면 트랜잭션이 Commit된 이후에 옵저버에 이벤트 메시지를 발행한다.(기본 설정이 AFTER_COMMIT 이므로)
    
    ```java
    @Component
    @Slf4j
    public class TicketCancelNotificationV2 {
       @Async("threadPoolTaskExecutor")
       @TransactionalEventListener
       publci void onTicketCancelHandler(ReservationInfo cancelMessage){
          log.info("Event Message => {}",cancelMessage);
       }
    }
    ```
![image](https://github.com/jeongye01/TIL/assets/74299317/c9286644-e741-4874-9605-e89d1c7ff33f)
