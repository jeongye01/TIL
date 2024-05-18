## **Singleton 패턴이란?**

단 하나의 유일한 객체를 만들기 위한 코드 패턴을 말한다.

**메모리 절약**을 위해, 인스턴스가 필요할 때 똑같은 인스턴스를 새로 만들지 않고 기존의 인스턴스를 가져와 활용해야 할 때 싱글톤 패턴을 사용한다.

디자인 패턴들 중에서 가장 개념적으로 간단한 패턴이다. 하지만, 우리는 간단하게 코드만 보고 넘어가는데 어디에 쓰이는지 어떠한 문제가 있는지 제대로 알아보자.

## **구조**

<img width="706" alt="image" src="https://github.com/jeongye01/TIL/assets/74299317/512a4273-a1c3-48e5-8111-d315d7dea974">


## **특징**

1. **생성자 함수의 접근 제한자가 private이다.**
    
    외부에서 new 키워드를 이용한 객체 생성을 막기 위해서 생성자 함수의 접근 제한자를 private으로 설정하고 객체의 초기화와 객체 가져오기는 오직! static public interface를 통해서만 가능하게 하기 위함이다.
    
2. **자신의 인스턴스를 저장하는 클래스 변수(static 필드) 가진다.**
    
    자신의 인스턴스를 저장하고 있는 클래스 변수를 가지고 있고 이 값이 null 일 경우에는 인스턴스를 생성하고 **클래스 변수에 인스턴스를 할당**한다. 그렇지 않은 경우에는 Heap 메모리에 저장되어 있는 인스턴스의 주소를 리턴한다.
    

## **싱글톤 패턴 구현 기법**

1. **Lazy initialization**
    
    ```java
    class Singleton {
        // 싱글톤 클래스 객체를 담을 인스턴스 변수
        private static Singleton instance;
    
        // 생성자를 private로 선언 (외부에서 new 사용 X)
        private Singleton() {}
    	
        // 외부에서 정적 메서드를 호출하면 그제서야 초기화 진행 (lazy)
        public static Singleton getInstance() {
            if (instance == null) {
                instance = new Singleton(); // 오직 1개의 객체만 생성
            }
            return instance;
        }
    }
    ```
    
    우리가 가장 많이? 흔하게? 사용하는 싱글톤 패턴이다.
    
    객체 생성에 대한 관리를 내부적으로 처리 메서드를 호출했을 때 인스턴스 변수의 null 유무에 따라 초기화 하거나 이미 생성되어 있는 인스턴스를 반환하는 기법이다.
    
    하지만, 스레드 세이프(Thread Safe)하지 않는 치명적인 단점을 가지고 있다.
    
    멀티 스레드 환경에서 각 스레드는 자신의 실행단위를 기억하면서 코드를 위에서 아래로 읽는다. 따라서 다음과 같은 동시성으로 인한 코드 실행 문제점이 발생 할 수 있게 된다.
    
    **>> 문제의 코드**
    
    1. 스레드 A, 스레드 B가 존재한다.
    2. 스레드 A가 if문을 평가하고 인스턴스 생성 코드로 진입한다.
    3. 그런데 그때 스레드 B가 if문을 평가한다. 아직 스레드 A가 인스턴스화 코드를 실행을 안시켰기 때문에 스레드 B가 평가한 if문은 true이다.
    4. 그러면 결과적으로 스레드 A와 B가 인스턴스 초기화 코드를 두번 실행하게 되는 결과를 볼 수 있다.
    
    ```java
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    
    // 싱글톤 객체
    class Singleton {
        private static Singleton instance;
    
        private Singleton() {}
    
        public static Singleton getInstance() {
            if (instance == null) {
                instance = new Singleton(); // 오직 1개의 객체만 생성
            }
            return instance;
        }
    }
    
    public class Main {
        public static void main(String[] args) {
            // 1. 싱글톤 객체를 담을 배열
            Singleton[] singleton = new Singleton[10];
    
            // 2. 스레드 풀 생성
            ExecutorService service = Executors.newCachedThreadPool();
    
            // 3. 반복문을 통해 10개의 스레드가 동시에 인스턴스 생성
            for (int i = 0; i < 10; i++) {
                final int num = i;
                service.submit(() -> {
                    singleton[num] = Singleton.getInstance();
                });
            }
    
            // 4. 종료
            service.shutdown();
    		
            // 5. 싱글톤 객체 주소 출력
            for(Singleton s : singleton) {
                System.out.println(s.toString());
            }
        }
    }
    ```
    
    **문제의 코드 실행 결과**
    
    <img width="662" alt="image" src="https://github.com/jeongye01/TIL/assets/74299317/d336fe31-9780-494c-ac40-c2a9ea48eb87">

    

1. **Bill Pugh Solution(LazyHolder)**

```java
class Singleton {

    private Singleton() {}

    public static Singleton getInstance() {
        return LazyHolder.INSTANCE;
    }
    // static 내부 클래스를 이용
    // Holder로 만들어, 클래스가 메모리에 로드되지 않고 getInstance 메서드가 호출되어야 로드됨
    private static class LazyHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
}
```

- 권장되는 두가지 방법 중 하나
- private static inner class를 사용하여 Thread Safe한 싱글톤 패턴을 구현하는 방법이다.
    - JVM의 ClassLoader에 의해서 클래스가 로드될 때 내부적으로 실행되는 synchronized 키워드를 이용하는 방법이다.
        - 최초로 ClassLoader에 의해서 로드 될 때 내부로 synchronized가 실행된다. 그래서 명시적으로 synchronized를 이용하지 않고 동일한 효과를 낼 수 있다. 해당 클래스(LazyHolder)는 static이므로 method가 실행될 때, JVM의 static initializer에 의해 초기화 되고, 메모리로 올라가게 됩니다.
            
            ```java
            protected Class<?> loadClass(String name, boolean resolve)
            	throws ClassNotFoundException
                {
            	synchronized (getClassLoadingLock (name )) {
            // First, check if the class has already been loaded
            Class<?> c = findLoadedClass (name) ;
            if (c == null) {
            // 이하 생략...
            ```
            
- 멀티쓰레드 환경에서 안전하고 Lazy Loading(지연 로딩; 나중에 객체 생성)도 가능한 완벽한 싱글톤 기법이다.
- static 메서드에서는 static 멤버만 호출할 수 있기 때문에 내부 클래스를 static으로 설정하고
이 밖에도 내부 클래스의 치명적인 문제점인 메모리 누수 문제를 해결하기 위하여 내부 클래스를 static으로 설정해야 한다.

<img width="694" alt="image" src="https://github.com/jeongye01/TIL/assets/74299317/467b15a7-1bf7-434f-b6a9-8fbd0c7ea00a">


**멀티 쓰레드 기반으로 실행하는 경우**

1. 첫 번째 스레드가 getInstance() 메서드를 호출하면 JVM은 LazyHolder 클래스를 메모리에 로드 하게 된다. 이미 메모리에는 올라갔으니, JVM은 LazyHolder 클래스를 한번만 로드한다.
2. 두 번째 스레드가 getInstance() 메서드를 호출하더라도, JVM은 두번 로드하지 않고 첫 번째 로드가 끝나고 초기화가 완료될 때까지 기다렸다가 작업을 진행한다.
3. 그래서 위의 방식과 다르게 두개의 인스턴스가 생성되지 않고 오직 하나의 객체, 진정한 싱클톤 방식처럼 인스턴스가 생성된다.

### **언제 사용하면 유용할까?**

리소스를 많이 차지하는 역할을 하는 무거운 클래스일때 적합하다. 대표적으로 데이터베이스 연결 모듈을 예로 들 수 있다. DB에 접속하는 작업(I/O 바운드)은 그 자체로 무거운 작업에 속하며 또한 한번만 객체를 생성하고 돌려쓰면 되기 때문에 사용할 때마다 생성할 필요가 없다.
