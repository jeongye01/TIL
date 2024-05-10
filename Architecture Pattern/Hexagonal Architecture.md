
![image](https://github.com/jeongye01/TIL/assets/74299317/4582a7ea-c07d-441f-b420-6bba259cc305)


## 헥사고날 아키텍처란?

육각형 형태로 디자인된 아키텍처로 유연함을 보여주며, 포트와 어댑터를 사용하는 것이 큰 특징이다. 

Layer간 원치 않는 종속성과 비즈니스 로직으로 인한 사용자 인터페이스 코드의 오염과 같은 객체 지향 소프트웨어 설계의 구조적 함정을 피할 수 있는 아키텍처이다.

- 관심사 분리 : 비즈니스 로직을 외부 종속에서 분리하는데 중점을 둔다.
- 코어 중심 설계: 핵심 어플리케이션을 아키텍처 중심에 두어 도메인 모델과 비즈니스 로직을 캡슐화하는 동시에 외부 시스템과 인터페이스는 포트와 어댑터를 통해 연결된다.
- 유연성 및 적응성 : 느슨한 결합으로 핵심 기능에 영향을 주지 않고 어댑터를 쉽게 교체하거나 수정할 수 있다.

## 용어 정리

1. Ports : DI를 위한 추상화 인터페이스
2. Adapters : 포트를 통해 인프라와 실제로 연결하는 부분만 담당하는 구현체
3. Domain Model : 실제 핵심 비즈니스 로직을 처리하는 부분
4. Domain Service(Use case) : 도메인 모델과 어댑터를 이용해서 비즈니스 로직과 인프라를 오케스트레이션하는 Dumb한 레이어

## 데이터 흐름

![image](https://github.com/jeongye01/TIL/assets/74299317/ce1905bb-b846-4b95-9a8f-09f7421d48d6)


### 기본적인 데이터의 흐름

(의존성 주입은 왼쪽에서 오른쪽으로 단방향임)

Client → Adapter → Port를 통해 적절한 Application Service에 전달 →Domain Model → if(DB connection || External API) 오른쪽에 있는 Adapter를 통해 처리 → if(isFeedback) 외부 처리 ㅗ안료 후, 다시 Application Service는 Domain Model 의 처리 결과를 받아 다시 Client에게 응답한다.

1. Adapters
    
    Adapter는 두 가지 종류가 있다. 사용자의 요청을 받아들일 때 사용하는 Primary adapter와 도메인 모델의 처리에 사용되는 Secondary adapter가 있다.
    
    - in/web/Primary adapter
        
        웹 서비스로 사용될 경우에는 Controller가 Primary dapter 역할을 한다. 사용자의 요청을 받는 컴포넌트 역할을 한다.
        
        사용자로부터 입력 받은 값의 유효성을 체크하고 전처리를 할 수 있다.
        
    - out/persistence/Secondary adapter
        
        외부(External; Application을 하나의 독립적인 공간으로 인식해야 한다. 그래서 Application를 제외한 모든 것들은 External이라고 보면 좋을 것 같다.)와의 통신이 필요한 도메인 모델을 연결할 수 있는 Adapter 역할을 한다. Primary adapter와 동일하게 외부와의 통신에 대한 구현만 담당한다. DB에 저장된 값을 조회하기 위해서 사용되는 PersistenceAdapter에게 요청을 보내야 한다.
        
2. Application
    1. Port
        1. In(w/ UseCase)
            
            단순한 인터페이스이며, 비즈니스 로직에 필요한 UseCase 인터페이스를 정의한다. 구현은 Service 레이어에서 하면 된다. 이렇게 나누는 이유는 Service에 대한 기능적인 확장이 발생했을 경우, 기존 코드를 수정하지 않고 런타임 시, Adapter가 의존하는 Service 인스턴스만 변경하도록 하기 위함이다.[OCP]
            
        2. Out
            
            단순한 인터페이스이며, 외부에 데이터를 요청하는데 사용되는 인터페이스를 정의한다. 기존 layerd 아키텍처에서는 Service 계층이 Repository 구현체에 의존하고 있었다면, Hexagonal 아키텍처에서는 Service 계층이 데이터를 요청하는 인터페이스에 의존한다. 구현체에 의존하지 않고 인터페이스에 의존하기 때문에 변경에 더 유연하다. [OCP]
            
    2. Service
        
        UseCase를 통해 비즈니스 로직을 구현한다. 그리고 처리하는 비즈니스 로직에서 DB 조회 또는 External API 호출이 필요하면 외부와의 통신을 위해서 Port/Out에 정의한 인터페이스를 사용하여 로직을 수행한다. 
        
3. Domain
    
    Domain Model에서 사용되는 DTO(Data Transfer Object)를 정의하는 패키지
    

![image](https://github.com/jeongye01/TIL/assets/74299317/9d000c98-bf77-4bd7-b5f5-8f92c7148e73)


## 언제 사용하면 유용할까?

레거시 코드를 사용하고 싶지만 새로운 인터페이스가 레거시 코드와 호환되지 않을 때

이미 만든 것을 재사용하고자 하나 이 재사용 가능한 라이브러리를 수정할 수 없을 때

이미 만들어진 클래스를 새로운 인터페이스(API)에 맞게 개조할때

소프트웨어의 구 버전과 신버전을 공존 시키고 싶을때
