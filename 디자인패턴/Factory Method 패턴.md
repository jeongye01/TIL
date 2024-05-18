## **Factory Method 패턴이란?**

### **Factory || Simple Factory 패턴?**

아래 예제 코드는 티켓 서비스에서 적절한 할인정책 객체를 생성해주는 팩토리 클래스이며, 우리가 흔히 말하는 팩토리 패턴의 예시 코드이다. 그런데 이것은 패턴은 아니고, 프로그래밍에서 자주 쓰이는 관용구 정도로 생각해야 한다. 진정한 팩토리 패턴은 “Factory Method” 패턴이다.

```java
package com.wanted.preonboarding.layered.application.ticketing.v3.policy;

import com.wanted.preonboarding.layered.domain.dto.PerformanceDiscountPolicyInfo;
import com.wanted.preonboarding.layered.infrastructure.repository.PerformanceDiscountPolicyRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;

import java.math.BigDecimal;
import java.util.UUID;
@Component
@RequiredArgsConstructor
public class DiscountPolicyFactory {
    private final PerformanceDiscountPolicyRepository performanceDiscountPolicyRepository;

    public DiscountPolicy create(UUID performanceId, int fixedPrice, String policyName) {
        PerformanceDiscountPolicyInfo info = PerformanceDiscountPolicyInfo.of(performanceDiscountPolicyRepository.findByPerformanceIdAndName(performanceId, policyName));
        return switch (policyName) {
            case "telecome" -> () -> info.getRate().multiply(new BigDecimal(String.valueOf(fixedPrice))).intValue(); // 비율 할인 -> 비율에 대한 정보
            case "new_member" -> () -> fixedPrice - info.getDiscountFee(); // 고정 금액 할인 -> 고정 금액 정보 ->
            default -> () -> 0;
        };
    }
}  
```

## **Factory Method 패턴?**

생성되는 **객체의 카테고리**는 다양하고 잦은 변화가 있지만, 일련의 기능(행동)은 고정 되어 있는 경우 Factory Method 패턴을 사용한다.

미스타 피자의 각 지점(한국, 인도, 프랑스)마다 **피자의 스타일**은 다르지만,
피자의 생성 과정(prepare → bake → cut → box)은 모두 동일하다.

Wanted Ticketing 서비스에서 고객 마다 적용하는 할인 정책의 종류는 다르지만,
할인 정책을 적용한 티켓 최종 가격을 계산하는 과정(정가 가격 - 할인금액 = 최종 가격 or 정가 가격 - 할인정책1 - 할인정책2 = 최종 가격)은 모든 고객이 동일하다.

### **구조**

<img width="696" alt="image" src="https://github.com/jeongye01/TIL/assets/74299317/d9292b1a-e8a7-477b-a68a-0f492fcf87d3">


1. **Creator || Factory**
    
    두 가지 메서드를 제공한다.
    
    첫 번째는 객체를 생성하는 메서드, 즉 팩토리 메서드용 인터페이스를 제공한다.
    
    두 번째는 팩토리 메서드에 의해 생산된 Concrete Product 객체로 Product를 구현한 모든 객체가 공통으로 해야하는 일련의 작업을 처리하는 퍼블릭 인터페이스를 제공한다.
    
2. **Concrete Creator**
    
    실제 Product를 생산하는 factoryMethod를 구현하는 역할을 한다.
    
3. **Product**
    
    Product 추상화 또는 인터페이스
    
4. **Concrete Product**
    
    Product의 구현체
    

### **특징**

위에서 이야기한 Factory 패턴의 원리(?)처럼 런타임 시점에 실시간으로 Factory의 구현체가 적절한 Prduct의 구현체를 생성하는 것이 아니라, 사용하는 서브클래스에 따라 생성되는 객체의 인스턴스(Product의 구현체)가 결정된다. 그런데 결국 여기서도 Factory 패턴을 사용하게 되어 있다.

Product의 구현체 생성을 캡슐화함으로써 구체적인 타입을 감추고 느슨한 결합 구조를 만든다.

### **작동 원리**

우리는 **티켓 할인 정책을 생성해주는** 팩토리를 먼저 결정하고, 파라미터로 전달 받은 Type 값을 이용하여 적절한 **할인 정책 객체**를 생성한다.

여기서 티켓 할인 정책을 생성 해주는 객체가 바로 Factory Method의 구현체(Concrete Creator)이고 이 구현체를 통해 생성된 할인 정책 객체가 바로 Product의 구현체이다.

특징에서 이야기 했던 부분을 좀 더 상세하게 이야기 하자면 첫 번째로, 어떤 종류(카드사 할인 || 통신사 할인 || 포인트 할인 || 자체 행사 할인)의 팩토리를 선택하냐에 따라서 생성될 수 있는 Product가 결정 되기 때문에 사용하는 서브 클래스에 따라 생성되는 Product가 결정된다.

두 번째로, 우리는 어떤 Product가 생성되는지 모른다. 하지만, Factory Method를 통해서 생성된 Factory를 통해 생성된 Product가 공통으로 해야 하는 일을 수행할 수 있는 객체가 생성되어 올바르게 동작할 수 있다는 사실만 알면 되기 때문에 Product의 내부 구현에 변경이 발생하더라도 Factory와 Client에게는 영향을 미치지 않는다.

- Main

```java
package com.wanted.preonboarding.layered.application.ticketing.v4;

import java.math.BigDecimal;

public class Main {
    public static void main(String[] args) {
        DiscountPolicyFactory f = new TelecomeDiscountPolicyFactory();
        BigDecimal finalFee = f.calculateTicketFee(10000, "kt");
        System.out.println(finalFee.toString());
    }
}
```

- Creator

```java
package com.wanted.preonboarding.layered.application.ticketing.v4;

import java.math.BigDecimal;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

// Creator
public abstract class DiscountPolicyFactory {
    public BigDecimal calculateTicketFee(int price, String type){
        DiscountPolicy discountPolicy = createDiscountPolicy(type);
        return discountPolicy.calculateDiscountFee(price);
    }

    protected abstract DiscountPolicy createDiscountPolicy(String type); // ## Factory Method ##
}
```

- Concrete Creator

```java
package com.wanted.preonboarding.layered.application.ticketing.v4;

import java.math.BigDecimal;
import java.util.NoSuchElementException;

public class TelecomeDiscountPolicyFactory extends DiscountPolicyFactory {
    @Override
    protected DiscountPolicy createDiscountPolicy(String type) {
        return switch (type.toLowerCase()) {
            case "lgu" ->
                new LGUDiscountPolicy(new BigDecimal(String.valueOf(1000)), new BigDecimal(String.valueOf(0.1)));
            case "skt" ->
                new SKTDiscountPolicy(new BigDecimal(String.valueOf(2000)), new BigDecimal(String.valueOf(0.1)));
            case "kt" ->
                new KTDiscountPolicy(new BigDecimal(String.valueOf(3000)), null);
            default -> throw new NoSuchElementException("Not found DiscountPolicy!");
        };
    }
}
```

- Product

```java
package com.wanted.preonboarding.layered.application.ticketing.v4;

import java.math.BigDecimal;
import java.util.Optional;

public abstract class DiscountPolicy {
    protected BigDecimal discountRate;
    protected BigDecimal discountFee;

    public BigDecimal calculateDiscountFee(int price) {
        if (Optional.ofNullable(discountRate).isPresent())
            return new BigDecimal(String.valueOf(price)).multiply(discountRate.multiply(new BigDecimal(String.valueOf(price))));
        return new BigDecimal(String.valueOf(price)).subtract(discountFee);
    }
}
```

- Concrete Product

```java
package com.wanted.preonboarding.layered.application.ticketing.v4;

import java.math.BigDecimal;

public class LGUDiscountPolicy extends DiscountPolicy {
    public LGUDiscountPolicy(BigDecimal discountFee, BigDecimal discountRate){
        this.discountFee = discountFee;
        this.discountRate = discountRate;
    }
}
```

```java
package com.wanted.preonboarding.layered.application.ticketing.v4;

import java.math.BigDecimal;

public class KTDiscountPolicy extends DiscountPolicy {
    public KTDiscountPolicy(BigDecimal discountFee, BigDecimal discountRate){
        this.discountFee = discountFee;
        this.discountRate = discountRate;
    }
}
```

```java
package com.wanted.preonboarding.layered.application.ticketing.v4;

import java.math.BigDecimal;

public class SKTDiscountPolicy extends DiscountPolicy {
    public SKTDiscountPolicy(BigDecimal discountFee, BigDecimal discountRate){
        this.discountFee = discountFee;
        this.discountRate = discountRate;
    }
}
```

### **언제 사용하면 유용할까?**

자바 세상에서는 new 연산자의 사용을 피할 수는 없다. 하지만, 주의 해야 할 점은 있다. 우리가 디자인 패턴을 배우면서 계속 이야기 했던 “변화는 것”과 “변화지 않는 것”을 구별하고 “변화는 것”은 캡슐화를 통해 느슨한 결합도를 유지해야 한다는 사실이다.

그래서 new 연산자를 통한 구현체에 의존할 때! 우리는 이것이 “변화는 것”인가? 그렇지 않은 것인가?를 먼저 질문하고 만약 “변화는 것”이라면 우리는 생성과 사용을 분리해야 하고 이러한 분리를 할 때 팩토리 메서드 패턴을 적용함으로써 불필요한 의존성을 제거 할 수 있다.
