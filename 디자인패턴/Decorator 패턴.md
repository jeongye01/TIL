## Decorator 패턴이란?

객체에 추가 요소(새로운 행동 or 기능)를 동적으로 더할 수 있는 패턴을 말한다. 데코레이터를 사용하면 서브클래스(상속)를 만들 때보다 훨씬 유연하게 기능을 확장할 수 있다,

### 구조

![image](https://github.com/jeongye01/TIL/assets/74299317/aeac7594-0e37-40c1-ad00-3ca46ba188a1)


1. Component; 모두의 슈퍼 클래스
    
    컴포넌트는 Wrapper(Base Decorator)들과 래핑되는 객체(Concreate Components Of Component)들 모두에 대한 공통 인터페이스를 선언한다.
    
2. Concrete Components; 구상 컴포넌트, 구현 컴포넌트
    
    슈퍼 클래스의 구현체로 기본 행동(Default)을 구현한다. 데코레이터 구현체에게 포장되는 객체(래핑되는 객체)를 말한다.
    
3. Base Decorator; 기초 데코레이터
    
    래핑되는 객체를 참조하기 위한 필드가 있다. 필드의 타입(Type)은 구상 컴포넌트와 구상 데코레이터를 모두 포함할 수 있는 Component 인터페이스 타입(다형성)으로 선언한다.
    
4. Concrete Decorators; 구현 데코레이터
    
    컴포넌트에 동적으로 추가될 수 있는 추가 행동(기능)들을 정의하는 클래스, 구현 데코레이터는 슈퍼 클래스의 추상 메서드(execute())에 추가 요소 (확장을 위한 추가 기능 or 행동)에 대한 부분을 정의한다.
    

### 특징

1. 데코레이터의 슈퍼클래스는 자신이 장식하고 있는 객체의 슈퍼 클래스와 같다
2. 한 객체를 여러 개의 데코레이터로 장식할 수 있다
3. 데코레이터는 자신이 장식하고 있는 개체(래핑되는 객체, wrappee)에게 어떤 행동을 위임(데코레이터는 합성을 통해서 자신이 장식하고 있는 객체의 특정한 메서드를 호출한다.) 하는 일 말고도 추가 작업(or 기능, 행동)을 추가 및 수행할 수 있다.
4. 서브클래스와 다르게 작은 단위로 기능을 구현한 데코레이터 집합체의 구상 구성요소를 런타임시점에 적절하게 조합이 가능하기 때문에 다양한 기능을 만들어 낼수 있는 유연성을 가진다.(OCP를 위반하지않음!)

### 작동 원리

1. 기본 기능을 수행하는 컴포넌트의 구상 클래스의 인스턴스를 생성
2. 1번에서 생성한 인스턴스를 이용하여 기본 기능 +a 로 필요한 추가 기능이 정의된 구상 데코레이터 인스턴스를 생성한다.
3. 구상 클래스와 구상 데코레이터의 공통으로 정의되어 있는 메서드(decorate)를 구상 데코레이터 인스턴스에서 호출한다.
4. 구상 객체부터 마지막 데코레이터 객체까지 순차적으로 메서드가 호출된다.(Like 재귀함수 호출)

![image](https://github.com/jeongye01/TIL/assets/74299317/5d5f2b43-6244-4e6a-a47b-c492266dbdf2)


## 패턴 적용 해보기

![image](https://github.com/jeongye01/TIL/assets/74299317/c9e04f7e-2707-4999-ab43-5a156f9badcd)


1. IComponent - Interface or Abstract
    
    ```java
    public interface Observer<T> {
       public boolean update(T message);
    }
    ```
    
2. Concrete Components
    
    ```java
    public class TicketCancelObserver implements Observer<CanceledTicketInfo>{
       public final TicketOffice office;
       
       @Override
       public boolean update(CanceledTicketInfo message){
          office.getSubscribers(message.getPerformanceId());
       }
    }
    ```
    
3. IDecorator - Interface or Abstract 
    
    ```java
    public abstract class MotificationDecorator<T> implements Observer<T> {
       Observer<T> t;
       protected abstract void sendMessage(T t);
    }
    ```
    
4. Concrete Decorator
    
    ```java
    public class KakaoNotification extends NotificationDecorator<CanceledTicketInfo>{
       public KakaoNotifiaction(Observer<CanceledTicketInfo> observer){
           this.t = observer;
       }
       @Override
       public boolean update(CanceledTicketInfo message){
          // 1, 추가 기능 실행
          sendMessage(message);
          // 2. 기존 기능 실행
          t.update(message);
          return true;
       }
       
       @Override
       protected void sendMessage(CanceledTicketInfo message){
           //추가요소(=기존 기능에 추가로 필요한 기능 또는 행동)
           System.out.println("[SEND to KakaoTalk]" + message);
       }
    }
    ```
    
    ## 언제 사용하면 유용할까?
    
    처음 설계 시, 페이스북이나 인스타그램 등 SNS 가 활성화 되지 않았던 사회라고 가정해보자. 그래서 알림 설정을 한 고객의 이메일로 알림을 전송하는 로직으로 개발을 진행했다.
    
    그런데 갑자기 FackBook, 카카오톡, 라인 그리고 인스타그램DM 이라는 서비스가 런칭되었고 이제 고객들은 이메일 뿐만 아니라 자신이 주로 사용하는 메신저로도 알림을 받고 싶어한다. 그렇다면 당신은 어떻게 할 것인가?
