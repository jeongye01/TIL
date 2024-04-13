# 람다(Lambda)
클래스 선언 없이 메서드를 정의할 수 있고, 이것을 값(Object) 처럼 사용할 수 있는 함수.

람다식은 익명 함수(이름이 없는 함수)를 표현하는 간결한 문법을 말한다.

람다식은 함수(자바세계에서는 Method로 이해하면 좋다.)를 일급객체(First-class object)로 취급하여 다른 함수의 인자로 전달하거나, 함수에서 반환 값으로 사용할 수 있다. 

(함수를 일급 객체로 잘 다루는 언어 → js)

자바8에서 람다 기능 제공.

## 일급 시민 객체

: Method or Function을 사용할때 다른 요소들과 아무런 차별이 없다는 것을 뜻한다.

### 1급 시민 객체가 되기 위한 조건 : 값으로 취급 할 수 있어야 한다.

- 모든 일급 객체는 변수가 데이터구조(ex List,Map)에 담을 수 있어야 한다.
- 모든 일급 객체는 메서드의 파라미터로 전달 할 수 있어야 한다.
- 모든 일급 객체는 메서드의 리턴 값으로 사용할 수 있어야 한다.

### 람다를 쓰는 이유

- 간결성
    - 복잡한 코드를 간결하게 표현함으로써 간결성과 가독성을 높일 수 있는 이점을 가지고 있다
- 익명 클래스 대체
    - 비즈니스 로직과 상관 없는 코드이면서 여러 곳에서 반복적으로 사용되는 코드(보일러플레이트)인 익명 클래스의 선언부를 제거할 수 있다.
        
        ```java
        // Anonymous Class Implement
        new Comparator<Apple>(){
            public int compare(Apple a1, Apple a2){
                return a1.getWeight().compareTo(a2.getWeight());
            }
        }
        
        // Lambda Expression
        (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())
        
        ```
        
- 스트림 API
    - 자바8에서부터 도입된 API로 컬렉션 처리를 간결하게 처리할 수 있도록 다양한 기능을 제공한다.
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/030f77a9-1ce1-4917-b029-3a1399122001/915c4d3a-79a5-4fbc-9288-750128d52653/Untitled.png)
        
    - 불변성 제공
    

### 함수형 인터페이스 : 람다를 제공하기 위한 문법

함수형 인터페이스(어노테이션)란 오직 하나의 추상 메서드를 정의하는 인터페이스다.

함수형 인터페이스(@FunctionalInterface)타입을 기대하는 곳 어디에서나 람다 표현식을 사용할 수 있다. 

**@FunctionalInterface 애너테이션**: 이 애너테이션은 해당 인터페이스가 함수형 인터페이스임을 명시

**예시1**

```java
Runnable r1 = () -> System.out.println("Runnable Concrete Instance -1");//익명 함수처럼 보이지만, 자바 컴파일러는 이 또한 Runnable 인스턴스로 취급한다.
// 그래서 Runnable 타입의 변수에 익명 함수가 할당 될 수 있다. 
Runnable r2 = new Runnable() {
   @Override
   public void run(){
      System.out.println("Runnable Concrete Instance - 2");
   }// 익명 클래스 문법 이용해서 클래스를 정의하고 new 키워드를 이용해서 정의한 클래스를 인스턴스화 시켰다.
};
// 
```

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달 할 수 있으므로 전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있다. 위의 예제 코드를 보면 Runnable 인터페이스를 구현하는 전통적인 방식(익명 클래스)와 람다식을 이용한 두가지 타입이 있다. 추상 메서드 구현을 직접 변수 할당 또는 메서드의 파라미터로 전달하는 방식은 Runnable 구현체, Runnable 인스턴스를 할당하는 것과 다를바가 없다. 

**예시2**

Predicate<String> isNotEmpty = s -> !s.isEmpty();
boolean result = isNotEmpty.test("hello"); // true 반환

자바의 **`java.util.function`** 패키지는 다양한 함수형 인터페이스를 제공합니다. 예를 들어, **`Predicate<T>`**는 타입 **`T`**의 객체를 인자로 받아 boolean 값을 반환하는 메서드 **`test`**를 정의합니다. 이것을 사용하여 특정 조건을 만족하는지 여부를 판단할 수 있습니다.

```java

Predicate<String> isNotEmpty = s -> !s.isEmpty();
boolean result = isNotEmpty.test("hello"); // true 반환
```

### 기본 구조

(parameters) → expression

(parameters) → {statements;}

: 선언형 문법 지향
