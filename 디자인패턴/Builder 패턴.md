## **Builder 패턴이란?**

복잡한 객체의 생성 과정과 표현 방법을 분리하여 다양한 구성의 인스턴스를 만드는 생성 패턴이다.

**구조**

생성자에 들어갈 매개 변수를 메서드를 통해서 하나하나 받아들이고 마지막에 통합 빌드해서 객체를 생성하는 방식이다.

1. **Data Class**
    
    데이터 구조를 가지는 클래스.
    
    ```java
    package com.study.design_pattern.builder;
    
    public class Developer {
        private int year;
        private String name;
        private String mainSkill;
        private String phoneNumber;
    
        public Developer(int year, String name, String mainSkill, String phoneNumber) {
            this.year = year;
            this.name = name;
            this.mainSkill = mainSkill;
            this.phoneNumber = phoneNumber;
        }
    
        @Override
        public String toString() {
            return "Developer { " +
                "year='" + year + '\'' +
                ", name=" + name +
                ", mainSkill=" + mainSkill +
                ", phoneNumber=" + phoneNumber +
                " }";
        }
    }
    ```
    

1. **Builder**
    
    Setter와 동일한 기능의 메서드를 이용하여 Data Class 필드의 적절한 값을 할당하고 인스턴스를 생성하는 역할을 가진다.
    
    ```java
    package com.study.design_pattern.builder;
    
    public class DeveloperBuilder {
        private int year;
        private String name;
        private String mainSkill;
        private String phoneNumber;
    
        public DeveloperBuilder year(int y){
            this.year = y;
            return this;
        }
        public DeveloperBuilder name(String n){
            this.name = n;
            return this;
        }
        public DeveloperBuilder mainSkill(String m){
            this.mainSkill = m;
            return this;
        }
        public DeveloperBuilder phoneNumber(String p){
            this.phoneNumber = p;
            return this;
        }
    
        public Developer build() {
            return new Developer(year, name, mainSkill, phoneNumber);
        }
    
    }
    ```
    

## **작동 원리**

빌더 패턴은 별도의 Builder 클래스를 선언하고 Builder 클래스에 정의한 **Setter 메서드를 통해서 순차적으로 생성자 파라미터의 값을 입력 받은 후**에 최종적으로 build() 메서드로 하나의 인스턴스를 생성하여 리턴하는 패턴이다.

```java
public class Main {
    public static void main(String[] args) {

        Developer developer1 = new DeveloperBuilder()
            .year(6)
            .name("유지노")
            .mainSkill("Spring")
						.phoneNumber("010-1234-1234")
            .build();
        System.out.println(developer.toString());
        System.out.println(developer1.toString());
    }
}
```

### 왜 이러한 구조가 나왔을까?

- 점층적 생성자 패턴 문제
    
    ```java
    package com.study.design_pattern.builder;
    
    public class Developer {
        private int year;
        private String name;
        private String mainSkill;
        private String phoneNumber;
    
    		public Developer() {}
    
        public Developer(int year) {
    		  this.year = year;
    		}
    
    		public Developer(String mainSkill, String phoneNumber) {
    			this.mainSkill = mainSkill;
          this.phoneNumber = phoneNumber;
    		}
    
        public Developer(int year, String name, String mainSkill, String phoneNumber) {
    	      this.year = year;
            this.name = name;
            this.mainSkill = mainSkill;
            this.phoneNumber = phoneNumber;
        }
    
        @Override
        public String toString() {
            return "Developer { " +
                "year='" + year + '\'' +
                ", name=" + name +
                ", mainSkill=" + mainSkill +
                ", phoneNumber=" + phoneNumber +
                " }";
        }
    }
    ```
    
- 자바 빈(Java Beans) 패턴 문제
    
    점층적 생성자 패턴 문제 해결을 하기 위해서 나온 패턴으로 매개변수가 없는 생성자로 객체를 생성한 후, Setter 메서드를 이용해 클래스 필드의 초깃값을 설정하는 방식이다. 하지만, 이 방식에는 2가지 문제가 있다.
    
    ```java
    package com.study.design_pattern.builder;
    
    public class DeveloperV1 {
        private int year;
        private String name;
        private String mainSkill;
        private String phoneNumber;
    
        public DeveloperV1() {
        }
    
        public void setYear(int y) {
            this.year = y;
        }
    
        public void setName(String n) {
            this.name = n;
        }
    
        public void setMainSkill(String m) {
            this.mainSkill = m;
        }
    
        public void setPhoneNumber(String p) {
            this.phoneNumber = p;
        }
    
        @Override
        public String toString() {
            return "Developer { " +
                "year='" + year + '\'' +
                ", name=" + name +
                ", mainSkill=" + mainSkill +
                ", phoneNumber=" + phoneNumber +
                " }";
        }
    
    }
    ```
    
    첫번째는, 의존성 주입 방식에 대해서 이야기할 때도 이야기 했었는데, Setter 메서드를 통해서 클래스 멤버(필드)를 초기화 하지 않고 퍼블릭 인터페이스를 호출한다면 필드 값의 null로 인해 치명적인 NPE가 발생할 수 있다라는 단점을 가지고 있다.
    
    두번째는, 불변성의 문제다. Setter 메서드는 객체를 처음 생성할때 필드 값을 설정하기 위해 존재하는 메서드이다. 하지만, 객체를 생성했음에도 여전히 외부에서 Setter 메서드를 노출하고 있기 때문에 객체의 필드 값은 언제 어디서든 변경 될 수 있다. 그렇다면 저자의 의도대로 클래스가 동작하지 않을 수도 있다.
    

### **특징**

1. **가독성이 높다.**
    
    생성자 방식으로 객체를 생성하는 경우는 매개변수가 많아질수록 가독성이 떨어진다. 생성자 함수의 인자의 갯수가 4개 이상 넘어가기 시작하면 대 혼란이 온다. 그래서 나는 생각보다 Builder 패턴을 이용한 객체 생성에 찬성하는 편이다.
    
    다만, 요즘 IDE의 Helper 기능들이 좋아져서 생성자 함수 호출 시, 파라미터 옆에 미리보기 힌트 기능을 제공하지만 이 또한 원시타입 값으로 제공해야 볼 수 있기 때문에 Builder 패턴이 더 좋은 것 같다.
    
2. **디폴트 매개변수 생략을 간접적으로 지원한다.**
    
    Builder 클래스에 디폴트 값(?)을 할당함으로써 Default 값을 정의할 수 있다.
    
    ```java
    function sayHello(user="Developrer"){
    	return "Welcome " + user;
    }
    ```
    
    - DeveloperBuilder 클래스
        
        ```java
        public class DeveloperBuilder {
            private int year;
            private String name = "유프링";;
            private String mainSkill = "java";
            private String phoneNumber;
        		
        		...
        }
        ```
        
    - main 클래스
        
        ```java
        public class Main {
            public static void main(String[] args) {
                Developer developer = new DeveloperBuilder()
                    .year(6)
                    .build();
                System.out.println(developer.toString());
            }
        }
        ```
        
    - 실행 결과
        
        Builder 객체의 메서드를 이용해서 year의 값만 초기화를 했는데, phoneNumber만 제외하고 나머지는 위에서 정의한 Default 값이 할당된 것을 볼 수 있다.
        
        **🚨여기서 잠깐!!!**
        
        밑에 결과를 보면 phoneNumber는 null이다. 왜? 위 main 클래스에서 보면 year 필드만 초기화를 위한 메서드를 호출하고 하고 있기 때문이다. 여기서 Builder 패턴(+ 룸북의 @Builder)의 문제점?이 무엇인지 생각이 드는가? 빌더를 통해서 객체를 생성하면 초기화를 해야 하는 필드(or 멤버)를 누락할 수 있는 가능성이 생기고 이로 인해 저자의 의도와 다르게 클래스의 기능이 제대로 동작하지 않고오류가 발생 할 수 있다라는 단점(?)을 가지고 있다.
        
        <img width="667" alt="image" src="https://github.com/jeongye01/TIL/assets/74299317/571b68b4-b453-401a-ba9d-e45fdb2cca1f">

        

1. **필수 멤버와 선택적 멤버를 분리 기능을 간접적으로 지원한다.**
    
    Builder 클래스를 통해 초기화가 필수인 멤버(필드)는 빌더의 생성자로 받게 하여 필수 멤버를 설정해줘서 Builder 객체가 생성되도록 유도하고, 선택적 사항은 Builder 객체의 메서드를 통해서 받으면 필수와 선택사항을 구분할 수 있게 된다.
    
    ```java
    public class DeveloperBuilder {
        private int year;
        private String name;;
        private String mainSkill;
        private String phoneNumber;
    
        public DeveloperBuilder(int y){
            this.year = y;
        }
    		
    		...
    }
    ```
    
    객체 생성 단계를 지연할 수 있다.
    
2. **필드에 대한 변경 가능성 최소화를 추구; 불변성**
    
    객체의 멤버를 초기화하는 과정은 다양하다. 그 중 우리가 흔하게 사용하는 방법 중 하나가 Setter 메서드 방식으로 객체의 필드 값을 초기화하는 방식을 사용한다. 하지만, Setter 메서드 방식에는 2가지 문제가 있다.(1. 필드가 불변성이 아닌 가변성을 가진다, 2. 초기화를 하지 않은 상태에서 메서드를 호출하면 NPE가 발생한다.)
    
    그래서 Builder 패턴은 **Setter 메서드를 사용하지 않고 builder 통한 생성자 함수만**을 이용함으로 필드에 대한 변경 가능성을 최소화 시켜주는 장점을 가진다.(완전한 불변성을 유지하는 것은 아니지만, 최소한의 불변성을 지키기 위한 노력 정도라고 봐주면 될 것 같다.)
    

## **심플 빌더 패턴(Effective Java)**

이펙티브 자바에서 소개한 빌더 패턴으로 GoF의 Builder 패턴과 구분하기 위해 “심플 빌더 패턴(Simple Builder Pattern)” 이라고도 부린다. 또는 우리가 잘 아는 룸북에서 제공하는 @Builder가 자동 생성? 해주는 빌더와 동일하다.

- **Data Class + Builder**

```java
package com.study.design_pattern.builder;

public class Developer {
    private int year;
    private String name;
    private String mainSkill;
    private String phoneNumber;

    private Developer(int year, String name, String mainSkill, String phoneNumber) {
        this.year = year;
        this.name = name;
        this.mainSkill = mainSkill;
        this.phoneNumber = phoneNumber;
    }

    public static Developer.Builder builder(){
        return new Developer.Builder();
    }

    public static class Builder {
        private int year;
        private String name;
        private String mainSkill;
        private String phoneNumber;

        public Builder year(int y){
            this.year = y;
            return this;
        }

        public Builder name(String n){
            this.name = n;
            return this;
        }
        public Builder mainSkill(String m){
            this.mainSkill = m;
            return this;
        }
        public Builder phoneNumber(String p){
            this.phoneNumber = p;
            return this;
        }

        public Developer build() {
            return new Developer(year, name, mainSkill, phoneNumber);
        }

    }

    @Override
    public String toString() {
        return "Developer { " +
            "year='" + year + '\'' +
            ", name=" + name +
            ", mainSkill=" + mainSkill +
            ", phoneNumber=" + phoneNumber +
            " }";
    }
}
```

- **main**

```java
package com.study.design_pattern.builder;

public class Main {
    public static void main(String[] args) {
        Developer newDeveloper = Developer.builder()
            .year(1)
            .name("new-type")
            .mainSkill("MZ")
            .phoneNumber("010-7777-7777")
            .build();

        System.out.println(newDeveloper.toString());
    }
}
```

**언제 사용하면 유용할까?**

생성자 함수의 파라미터가 많은 경우 또는 코드의 가독성을 높이고 싶을 사용하면 유용하다. 하지만, 지나친 남용은 성능 상의 이슈가 발생할 수도 있고 코드의 복잡성도 올라간다라는 단점을 가지고 있다.
