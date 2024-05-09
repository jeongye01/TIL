## 적용된 객체지향 이론 규칙

- 구현보다는 **추상화 또는 인터페이스**에 의존해서 코딩해라
- 상속보다는 구성(합성 or 위임): Composition 활용해라
    - “A는 B이다(is - A)” 보다 “A에는 B가 있다”가 더 좋을 수 있다.

## Adaptor 패턴이란?

(브릿지; 브로커 → 한 객체의 인터페이스를 다른 객체가 이해할 수 있도록 변환하는 특별한 객체를 말한다.) 어댑터는 한 인터페이스(기존 시스템)를 다른 인터페이스(업체에게 제공한 클래스)로 변환해 주는 역할을 한다. 어댑터는 소켓의 인터페이스를 플러그(External API)에서 필요로 하는 인터페이스로 바꿔준다고 할 수 있다. 어떤 인터페이스를 클라이언트에서 요구하는 형태로 적응(=mirror)시키는 역할을 한다.

![image](https://github.com/jeongye01/TIL/assets/74299317/30113515-7837-4715-9e37-7154ea4dd988)



## 구조

### 객체 어댑터(Object Adaptor)

![image](https://github.com/jeongye01/TIL/assets/74299317/39df388b-d669-4f07-85d7-6491dd778fc6)


1. **Client**
    
    프로그램의 기존 비즈니스 로직을 포함하는 클래스
    
    → Spring
    
2. **Target || ClientInterface**
    
    Adapter가 구현하는 인터페이스
    
    → java.sql.Driver
    
3. **Adapter**
    
    Client와 Adaptee(Service) 중간에서 호환성이 없는 둘을 연결 시켜주는 역할을 담당 한다.
    
4. **Service; Adaptee**
    
    일반적으로 타사 라이브러리 또는 레거시의 유용한 클래스를 뜻한다. 클라이언트는 서비스 클래스를 직접 사용할 수 없다. 왜냐하면 서비스 클래스가 Target  Interface와 호환되지 않는 데이터 타입 또는 인터페이스를 갖고 있기 때문이다.
    
    → org.apache.elasticsearch.jar
    

## 특징

프로그램의 기본 비즈니스 로직에서 인터페이스 또는 데이터 변환 코드를 분리할 수 있기 때문에 단일 책임 원칙(SRP)을 만족한다.

![image](https://github.com/jeongye01/TIL/assets/74299317/7a852a9a-377f-4560-b0d5-7e90b2702f21)


한국 거래소에서는 증권 데이터를 XML 형태로 제공한다. 그래서 우리는 XML 데이터 기반으로 분석할 수 있는 A업체의 분석 라이브러리를 이용하여 증권 데이터를 분석하고 있었다. 그런데 초기 버전이라서 그런지 오류도 많고 성능도 좋지 않다. 이제 곧 계약이 종료 되니, 다른 업체를 찾아봐야 겠다.

그런데 한가지 고민이 있다. 분석 라이브러리 업계에서는 대부분 JSON 타입을 기반으로 분석한다는 것이다. 그런데 한국거래소에서는 XML 형식으로 데이터를  제공한다.

우리가 개발 실무를 하다 보면 자주 겪는 문제이다. 그래서 우리는 호환되지 않는 형식이라는 딜레마를 해결하기 위해 Adapter 패턴을 사용한다.

위와 같은 상황에서 증권 데이터를 가져와서 XML 에서 JSON 형식으로 변환 해주는 Adapter를 만들 수 있다. 그런 다음 이러한 어댑터를 통해서만 라이브러리와 통신하도록 코드를 수정한다. Adapter가 Core Class(Client Class)가 보낸 메시지를 수신하면 들어오는 XML 데이터를 JSON 구조로 변환하고 메시지를 래핑된 분석 객체(위임한 객체)의 적절한 메서드에게 전달한다. 

이렇게 하면 분석 라이브러리는 한 가지의 책임만을 가지면 퍼블릭 인터페이스와 데이터 변환 코드를 분리 할 수 있다.

![image](https://github.com/jeongye01/TIL/assets/74299317/b69dc6f9-2035-49af-ad3d-8c1aca5306c5)


## 작동 원리

Spring Boot에서 다양한 RDBMS 와의 호환을 위해서 공통 인터페이스(java.sql.Driver)를 제공한다. 그래서 MySQL이나 Oracle에서 제공하는 jar 파일에는 각각의 RDB에서 Driver 인터페이스를 구현한 구현체가 있다.

여기서 Adapter 패턴의 시선으로 보면 SpringBoot가 위에서 이야기한 Client의 역할이고 Client Interface가 java.sql.Driver이다. 그리고 com.mysql.cj.jdbc.Driver 이 공통 인터페이스의 구현체이다. 그런데 아직 Adapter와 Adaptee가 안보인다. 밑에서 알아보자

그런데 과거에는 RDB만 사용했는데, 반도체의 가격인하와 기술 발전으로 메모리와 디스크의 가격이 떨어지고 이로 인해, IoT의 기술이 크게 발전하였고 하나의 디바이스가 발생하는 데이터의 양이 과거보다 기하급수적으로 늘어나게 되었다. 

그래서 빅데이터를 좀 더 빠른 검색, 저장, 삭제를 하기 위해서 RDB와는 전혀 다른 NoSQL DB가 탄생하기 시작한다, 그래서  SpringBoot 진영에서도 NoSQL을 지원해야 하는데 다른 issue로 전혀 신경을 써주지 못하고 있다. 그래서 NoSQL의 대표 정도 되는 Elasticsearch 재단에서 NoSQL 과 호환될 수 있는 Adapter와 Adaptee를 만들었다. Adapter는 java.sql.Driver를 구현하는 객체이며, 실질적인 기능의 동작은 Adaptee(Elasticsearch.java)에서 동작하고 결과값을 return 한다.

![image](https://github.com/jeongye01/TIL/assets/74299317/515175cb-c602-4e04-b575-3dab0b79d7e6)



+추가예시코드)
```
// Target 인터페이스
interface USPlug {
    void provideElectricity();
}

// Adaptee 클래스
class KoreanPlug {
    void provide220V() {
        System.out.println("Providing 220V from a Korean plug");
    }
}

// Adapter 클래스
class PlugAdapter implements USPlug {
    KoreanPlug koreanPlug;

    public PlugAdapter(KoreanPlug koreanPlug) {
        this.koreanPlug = koreanPlug;
    }

    @Override
    public void provideElectricity() {
        koreanPlug.provide220V();  // 220V 전원을 제공받아 USPlug 인터페이스로 변환
        System.out.println("Adapter converts 220V to 120V for US appliances");
    }
}

// 클라이언트 코드
public class AdapterDemo {
    public static void main(String[] args) {
        KoreanPlug koreanPlug = new KoreanPlug();
        USPlug adapter = new PlugAdapter(koreanPlug);
        adapter.provideElectricity();  // 어댑터를 통해 미국 기기에 전원을 제공
    }
}


```
