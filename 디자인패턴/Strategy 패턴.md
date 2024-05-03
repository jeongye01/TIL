### 적용된 객체지향 이론 규칙

- 구현보다는 **추상화 또는 인터페이스에 의존**하라
- 상속보다는 구성(합성 or 위임) : Composition 활용해라 (”A는 B이다.: 보다 “A에는 B가 있다.”가 더 좋을 수있다.)

### Strategy 패턴이란?

실행(Runtime, 런타임) 중에 알고리즘 전략을 선택하여 객체 동작을 실시간으로 바뀌도록 할 수 있게 하는 행위 디자인패턴이다. 

모든 영역의 개발세계에는 하나의 진리가 있는데, 아무리 디자인을 잘한 애플리케이션이라도 시간이 지남에 따라 변화하고 성장해야 한다는 사실이다. 그래서 우리는 항상 변화와 함께한다.

그래서 우리는 설계단계에서 변하는 것과 변하지 않는 것을 구분해야 한다. 변하지 않는 것은 그대로 두고 변하는 것은 찾아서 나머지 코드에 영향을 주지 않도록 캡슐화를 해야한다.

이러한 이론을 기반으로 정의된 것이 전략 패턴이다. 변경되는 부분은 캡슐화를 통해서 확장에는 열려 있고 수정에는 닫혀있는 구조로 런타임 시점에 적절한 알고리즘 전략을 선택하여 어플리케이션이 유연하게 동작 할 수 있도록 하는 것이다.

### 캡슐화?

단순히 내부 구현을 숨기는게 캡슐화인가?

```java
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class YourAction {
   private String name;
   private int action // 0: write annotation Getter 1:write annotation Setter
   // 이 코드가 진정한 캡슐화 
   // 고객은 우리의 의도와 다르게 행동한다.
   public void setDoItAction(int i){
      if(!(i==0 || i==1)){
        action = i;
      }
   }
   
   public static void main(String[] args){
       YourAction a = new YourAction();
       // 간접적으로 action 필드의 존재 여부
       // 저자 (YourAction 개발자)의 의도와 다른 값을 할당
       // 단순히 setter를 제공하는 것이 반드시 캡슐화라고 할 수 없다.
       a.setAction(3); // 이러면 어떻게 될까?
   }

}
```

### 전략이란?

전략은 특정한 목적을 수행하기 위한 행동 계획(알고리즘 or 기능 or 동작)을 말한다.

![image](https://github.com/jeongye01/TIL/assets/74299317/1f1d23e5-e42a-41f8-874b-d5c91ba400e2)


### 코드 구현 by native java

1. IStrategy;전략 인터페이스
    
    전략구현체에 대한 공용 인터페이스.
    
    ```java
    public interface IStrategy {
        void execute();
    }
    ```
    
2. ConcreateStrategies;전략 구현체
    
    전략 인터페이스 기반으로 알고리즘, 기능,동작을 객체로 정의한 구현체.
    
    ```java
    public class FirstStrategy implements IStrategy {
       @Override
       public void execute(){
          System.out.println("First Strategy");
       }
    
    }
    public class SecondStrategy implements IStrategy {
       @Override
       public void execute(){
          System.out.println("Second Strategy");
       }
    
    }
    ```
    
3. Context
    
    상황에 맞는 알고리즘을 구현한 구현체의 메서드를 호출하는 객체.
    
    ```java
    public class Context{
       private IStrategy strategy;
       
       //생성자를 이용한 방식
       public Context(IStrategy s){
          this.strategy = s;
       }
       // Setter를 이용한 주입 방식
       public void setStrategy(IStrategy st){
          this.strategy = st;
       }
       public void doSomething(){
          this.strategy.execute();
       }
       
       //Method를 이용한 주입 방식
        public void doSomething(IStrategy externalStrategy){
          externalStrategy.execute();
       }
    }
    ```
    
     **Setter 주의점**
    
    실무에서는  setter를 잘 사용하지 않는다. setter를 호출하지 않고 퍼블릭 인터페이스를 먼저 호출하면 NPE(Null Point Exception)이 발생할 수 있음. 그리고 생성과 사용은 분리하지 않아야한다. 생성과 사용을 따로두면 결합도가 올라감. 왜냐하면 생성하기 위해서는 객체의 타입과 생성자에 전달해야하는 인자에 대한 과도한 지식을 알아야하기 때문이다.
    
    생성과 사용에 책임 분리가 필요하고 우리는 이럴때 Factory 패턴을 사용할 수 있다. 
    
4. Client
    
    
    ```java
    public class Client {
        public void doIt(){
           //constructor 방식
           //결국은 이 방식도 FirstStrategy 구현체에 의존하고 있는 상태
           // First에서 Second 전략으로 수정하려면 코드를 수정해야 한다.
           Context ctx = new Context(new FirstStrategy());
           ctx.doSomthing();
        }
        public void doIt(String selectedStrategyName){
           //Setter 방식
           //Setter는 선택된 전략의 이름을 받아서 적절한 전략을 생성하고 설정해서
           //문맥에 맞는 목표를 달성한다.
           Context ctxSetter = new Context();
           ctxSetter.setStrategy(createStartegyByName(selectedStrategyName));
           ctxSetter.doSomething();
        }
        public void doIt(IStrategy s){
           //Method of parameter 방식
           Context ctx = new Context();
           ctx.doSomething(s);
        }
        private IStrategy createStrategyByName(String selectedStrategyName){
           switch(selectedStrategyName){
               case "first":
                   return new FirstStrategy();
               case "second":
                   return new SecondStrategy();
               default:
                   return new DefaultStrategy();
           }
        }
        
    }
    ```
    
    1. Factory
        
        ```java
        public class Factory {
           public Context createContext(String strategy){
               IStrategy selected = createStrategy(strategy);
               return new Context(selected);
           }
           private IStrategy createStrategy(String strategy){
               return switch(strategy){
                   case "first" -> new FirstStrategy();
                   case "second" -> new SecondStrategy();
                   default -> new DefaultStrategy();
               }
           }
        }
        ```
        

### 코드 구현 by Spring

사과의 품질을 나누는 기준. 작년에는 무게를 기준으로 상품의 가치를 나눴는데, 올해는 색상으로 나눈다고 한다.

1. IStrategy; 전략 인터페이스
    
    (사과 기준을 나누는)전략 구현체에 대한 공용 인터페이스. 전략 구현체의 이름을 리턴하는 공용 메서드를 선언하기 위해서 인터페이스에서 추상체로 변경
    
    ```java
    public abstract class ApplePredicate {
       public String getFilterName() {
          return getClass().getSimpleName();
       }
       public abstract boolean filter(Apple apple);
    }
    ```
    
2. ConcreateStrategies; 전략 구현체
    
    사과를 특정한 기준으로 구분해내는 기능이 구현되어 있는 구현체.
    
    구현체 1
    
    ```java
    @Component
    public class AppleColorPredicate extends ApplePredicate {
      @Override
      public boolean filter(Apple apple){
         return GREEN.equals(apple.getColor());
      }
    }
    ```
    
    구현체 2
    
    ```java
    @Component
    public class AppleHeavyWeightPredicate extends ApplePredicate {
      @Override
      public boolean filter(Apple apple){
         return apple.getWeight() > 15; // 실제 운영 환경에서는 15라는 값을 DB에 저장해놓고 갖다 사용한다.
         // 이값으로 인해 코드를 수정하고 다시 배포를 해야하는 상황방지
      }
    }
    ```
    
3. Context
    
    Spring ApplicationContext를 이용해서 전략 구현체들을 보관하고 있다가, Client의 요구에 따라 적절한 전략 구현체를 리턴하는 역할.
    
    ```java
    @Component
    @RequiredArgsConstructor
    public class PredicateFactory {
      //자동으로 구현체로 들어옴(스프링이 해줌)
      private final Set<ApplePredicate> applePredicateSet;
      private Map<String,ApplePredicate> predicate;
      
      @PostConstruct
      public void init(){
         createPredicate(applePredicateSet)
      }
      private void createPredicate(Set<ApplePredicate> predicateSet){
         predicate = new HashMap<String,ApplePredicate>();
         predicateSet.forEach(p->predicate.put(p.getFilterName(),p));
      }
      public ApplePredicate findBy(String className){
         return predicate.get(className);
      }
    }
    ```
    
4. Client
    
    ```java
    @Service
    @RequiredArgsConstructor
    public class AppleService {
       private final PredicateFactory factory;
       
       public List<Apple> getFilteredAppleList(List<Apple> appleList, String className){
           List<Apple> result = new ArrayList<Apple>();
           ApplePredicate predicate = factory.findBy(className);
           for(Apple apple : appleList){
              if(predicate.filter(apple))
                  result.add(apple);
           }
           return result;
       }
    }
    ```
    
    ```java
    @AllArgsConstructor
    @Builder
    @Getter
    @Setter
    public class Apple {
       private Color color;
       private Long weight;
    }
    ```
    
    ```java
    
    public enum Color {
       RED, BLUE, GREEN;
    }
    ```
    

Spring와  Java의 전략패턴 구조가 약간 다르다. Spring에서는 ApplicationContext라는 객체가 있고, 이 객체 안에는 ConcreateStrategies 전략 구현체들이 담겨있다. 그래서 Java와 다르게 전략 구현체를 인스턴스화하는 Factory 클래스가 필요없다. (다만, 구현체들이 Bean 객체로 등록이 되어있다라는 가정이 있다.)

### 언제 사용하는게 유용할까?

어떤 일을 수행하는 알고리즘이 다양하고 미리 정의 해놓은 알고리즘을 손쉽게 변경할 수 있어야 하고 변경이 빈번하게 필요한 경우에 적합한 패턴이다.
