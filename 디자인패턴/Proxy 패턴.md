## **Proxy** 패턴이란?

특정 객체로의 접근을 제어하는 대리인(특정 객체를 대변하는 객체)을 제공하는 패턴을 말한다.

## 구조

![image](https://github.com/jeongye01/TIL/assets/74299317/a3a8a790-6575-4cb6-9ea0-3bc0531c6378)


프록시는 **다른 객체에 대한 접근을 제어하는 객체**이다. 여기서 다른 객체를 대상(Subject)라고 부른다. 프록시와 대상은 동일한 인터페이스를 가지고 있으며 이를 통해 다른 인터페이스와 완전히 호환되도록 바꿀 수 있다.

1. **Subject**
    
    Proxy와 RealSubject를 하나로 묶는 인터페이스(다형성). 
    
2. **RealSubject**
    
    원본 대상 객체
    
3. **Proxy**
    
    Client와 대상 객체(RealSubject)를 중계할 대리인 역할
    
    프록시는 대상 객체를 합성한다.
    
    프록시는 대상 객체와 같은 이름의 메서드를 호출하며, 별도의 로직을 수행 할 수 있다. 별도의 로직이라 함은 대상 객체를 호출 할 수 있는 권한 또는 유효성 검사 등과 같은 로직이 될 수 있다.
    
4. **Client**
    
    Subject 인터페이스를 이용하여 프록시 객체를 생성해 이용하는 역할
    

## 특징

기존 대상 객체의 코드를 변경하지 않고 새로운 기능을 추가할 수 있다.

데코레이터 패턴과 유사하다. 기능 확장에 필요한 새로운 기능을 추가하기 위해서 대상 객체(Subject)의 코드는 수정하지 않고(수정에는 닫혀있음) Proxy 인터페이스에 새로운 기능을 추가하고 대상 객체의 인터페이스를 호출하는 방법으로 추가가 가능하다.

대상 객체는 자신의 기능에만 집중하고, 그 이외 부가 기능을 제공하는 역할은 프록시 객체에 위임함으로 단일 책임 원칙을 위반하지 않는다.

접근 권한이나 생성에 대한 부분을 프록시에게 모두 위임하고 자신이 가지는 단일 책임만 수행한다.

## 작동 원리

대상 객체(Subject)의 메서드를 직접 실행하는 것이 아니라, 대상 객체에 접근하기 전에 프록시 객체의 메서드를 접근 한 후 추가적인 로직을 처리한 뒤 접근하게 된다.

간단하게 보면 Client → Proxy → After Operating Method of Proxy, Subject 순서가 된다.

> 예시 코드 출처; https://inpa.tistory.com/entry/GOF-💠-프록시Proxy-패턴-제대로-배워보자 [Inpa Dev 👨‍💻:티스토리]
> 

### 기본형 프록시

```java
interface ISubject {
    void action();
}

class RealSubject implements ISubject {
    public void action() {
        System.out.println("원본 객체 액션 !!");
    }
}

```

```java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 composition

    Proxy(RealSubject subject) {
        this.subject = subject;
    }

    public void action() {
        subject.action(); // 위임
        /* do something */
        System.out.println("프록시 객체 액션 !!");
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy(new RealSubject());
        sub.action();
    }
}
```

### 가상 프록시

**Lazy Constructor(지연 초기화) 방식**

가끔 필요하지만, 항상 메모리에 적재되어 있는 무거운 서비스 객체가 있는 경우

이 구현은 실제 객체의 생성에 많은 자원이 소모되지만, 사용 빈도는 낮을 때 적용하는 방식

```java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 composition

    Proxy() {
    }

    public void action() {
    	// 프록시 객체는 실제 요청(action(메소드 호출)이 들어 왔을 때 실제 객체를 생성한다.
        if(subject == null){
            subject = new RealSubject();
        }
        subject.action(); // 위임
        /* do something */
        System.out.println("프록시 객체 액션 !!");
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy();
        sub.action();
    }
}

```

### 보호 프록시

프록시가 대상 객체에 대한 자원으로의 엑세스 제어

특정 클라이언트만 서비스 객체를 사용할 수 있도록 해야하는 케이스에 적용! 하지만, Spring에서는 이러한 부분을 이미 인터페이스로 제공**(Filter/Intercepter/AOP)**

프록시 객체를 통해 클라이언트의 자격 증명이 기준과 일치하는 경우에만 서비스 객체에게 요청 메세지를 전달할 수 있게 한다.

```java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 composition
    boolean access; // 접근 권한

    Proxy(RealSubject subject, boolean access) {
        this.subject = subject;
        this.access = access;
    }

    public void action() {
        if(access) {
            subject.action(); // 위임
            /* do something */
            System.out.println("프록시 객체 액션 !!");
        }
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy(new RealSubject(), false);
        sub.action();
    }
}
```

## 언제 사용하면 유용할까?

기능 확장보다는 서비스 클래스를 사용하기 직전에 인스턴스를 생성하는 **지연 로딩 방식**이나 서비스 클래스의 메서드가 실행되기 전에 접근 권한 확인 또는 파라미터 유효성 체크, 로깅 처리 등 서비스 클래스의 메서드 실행 전에 실행을 해야하는 케이스에서 사용하면 좋다.

실무에서는 전문API 통신 시, 많이 사용된다. 전문을 만들고 전문API 서버로 직접 통신 하는 방식이 아니라 프록시 서버를 통해서 인증 절차를 진행한 후에 요청한 전문이 정상적으로 전문API 서버로 전달되고 응답을 받을 수 있다.
