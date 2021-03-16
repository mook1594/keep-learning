#### [GO TO BACK](../README.md)

# 2. 아키텍처 개요
> 아키텍처  
> DIP  
> 도메인 영역의 주요 구성요소  
> 인프라스트럭처  
> 모듈  

### 계층 구조 아키텍처
- 표현, 응용, 도메인, 인프라스트럭처 계층으로 이루어짐
- 인프라 스트럭처에 의존하면 테스트 어려움, 기능 확장의 어려움 두가지 문제가 있다.
- 이를 해결하는 방법는 DIP를 적용하는 것이다.
```java
public class DroolsRuleEngine {
    private KieContainer kContainer;

    public DrollsRuleEngine() {
        KieServices ks = KieServices.Factory.get();
        kContainer = ks.getKieClasspathContainer();
    }

    public void evalute(String sessionName, List<?> facts) {
        KieSession kSession = kContainer.newKieSession(sessionName);
        try {
            facts.forEach(x -> kSession.insert(x));
            kSession.fireAllRules();
        } finally {
            kSession.dispost();
        }
    }
}

public class CalculateDiscountService {
    private DroolsRuleEngine ruleEngine;
    public CalculateDiscountService() {
        ruleEngine = new DroolsRuleEngine();
    }

    public Money calculateDiscount(List<OrderLine> orderLines, String customerId) {
        Customer customer = findCustomer(customerId);
    
        MutableMoney money = new MutableMoney(0);
        List<?> facts = Arrays.asList(customer, money);
        facts.addAll(orderLines);
        ruleEngine.evalute("discountCalculation", facts);
        return money.toImmutableMoney();
    }
}
```

### DIP
- 상위가 되는 고수준 모듈과 하위가 되는 저수준 모듈에서 고수준 모듈이 저수준 모듈에 의존되면 앞서 두가지 문제가 발생한다 (테스트 어려움, 구현변경 어려움)
- DIP는 저수준이 고수준을 의존하도록 한다. (추상화 인터페이스 활용)
```java
public interface RuleDiscounter {
    public Money applyRules(Customer customer, List<OrderLine> orderLines);
}

public class CalculateDiscountService {
    private CustomerRepository customerRepository;
    private RuleDiscounter ruleDiscounter;
    
    public CalculateDiscountService(CustomerRepository customerRepository, RuleDiscounter ruleDiscounter) {
        this.customerRepository = customerRepository;
        this.ruleDiscounter = ruleDiscounter;
    }   
    
    public Money calculateDiscount(List<OrderLine> orderLines, String customerId) {
        Customer customer = findCustomer(customerId);
        return ruleDiscounter.applyRules(customer, orderLines);
    }

    private Customer findCustomer(String customerId) {
        Customer customer = customerRepository.findById(customerId);
        if (customer == null) throw new NoCustomerException();
        return customer;
    }
}

public class DroolsRuleDiscounter implements RuleDiscounter {
    private KieContainer kContainer;

    public DroolsRuleDiscounter() {
        KieServices ks = KieServices.Factory.get();
        kContainer = ks.getKieClasspathContainer();
    }

    public Money applyRule(Customer customer, List<OrderLine> orderLines) {
        KieSession kSession = kContainer.newKieSession("discountSession");
        try {
            kSession.fireAllRules();
        } finally {
            kSession.dispose();
        }
        return money.toImuutableMoney(); 
    }   
}
```
- 고수준의 모듈이 저수준의 모듈을 사용하려면 고수준의 모듈이 저수준 모듈을 의존해야하는데, 위에는 저수준의 모듈이 고수준의 모듈을 의존한다고 해서 이를 DIP 라고한다.
- 저수준을 모듈을 변경해도 고수준 모듈을 변경할 필요가 없다.

```java
public class CalculateDiscountServiceTest {
    @Test(expected = NoCustomerException.class)
    public void noCustomer_thenExceptionShouldBeThrown() {
        CustomerRespository stubRepo = mock(CustomerRepository.class);
        when(stubRepo.findById("noCustId")).thenReturn(null);

        RuleDiscounter stubRule = (cust, lines) -> null;
        CalculateDiscountService calDisSvc = new CalculateDiscountService(stubRepo, stubRule);
        calDisSvc.calculateDiscount(someLines, "noCustId");
    }
}
```
#### DIP 주의사항 
- DIP는 단순 인터페이스와 구현 클래스를 분리하는것이 아님.
- 고수준 모듈이 저수준 모듈에 의존 하지 않도록 하기 위함
- 저수준 모듈에서 인터페이스를 추출하는 경우는 잘못된 경우이다.

#### DIP와 아키텍처
- 인프라스트럭처 영역은 저수준 모듈
- 응용, 도메인 영역은 고수준 모듈

### 도메인 영역의 주요 구성요소
- 엔티티 (Entity): 도메인의 고유한 개념, (주문, 회원 상품)
- 밸류 (Value): 개념적으로 하나인 도메인 객체를 표현, (주소, 금액, 주문라인, 주문자)
- 애그리거트 (Aggregate): 개념적으로 하나로 묶는것, (주문 엔티티, 오더라인 밸류, 주문자 밸류를 주문 애그리거트로 묶음)
- 리포지터리 (Repository): 도메인 모델의 영속성을 처리
- 도메인 서비스 (Domain Service): 특정 엔티티에 속하지 않는 도메인 로직을 제공, (할인 금액 계산은 상품, 쿠폰, 회원등급, 구매금액 다양한 조건을 이용함)

#### 엔티티와 밸류
- DB모델의 엔티티와, 도메인 모델의 엔티티는 다르다
- 가장 큰 차이점은 도메인 모델의 엔티티는 데이터와 함께 도메인 기능을 함께 제공
- ex) 주문을 표현하는 엔티티는 주문과 관련된 데이터 뿐 아니라 배송지 주소 변경을 위한 기능을 함께 제공한다.
##### 도메인 모델에서의 주문 엔티티
```java
public class Order {
    private OrderNo number;
    private Orderer orderer;
    private ShippingInfo shippingInfo;

    public void changeShippingInfo(ShippingInfo newShippingInfo) {
        checkShippingInfoChangeable();
        setShippingInfo(newShippingInfo);
    }
    private void setShippingInfo(ShippingInfo newShippingInfo) {
        if(newShippingInfo == null) throw new IllegalArguementException();
        // 밸류 타입의 데이터를 변경할 때는 새로운 객체로 교체한다.(불변 권장)
        this.shippingInfo = newShippingInfo;
    }
}
```
- 도메인 모델에서는 데이터와 함께 기능을 제공한다.
- 도메인 모델에서는 Orderer와 같이 밸류타입을 이용해서 표현가능하다. Orderer는 이름과 이메일 데이터를 포함 할 수 있다. DB모델에서는 테이블내에 컬럼을 2개를 두거나 다른테이블에 관계 테이블로 만들어야한다.
#### 애그리거트
- 관련 객체를 하나로 묶은 군집
- ex) 주문, 배송지정보, 주문자, 주문목록, 총결제 금액
- 애그리거트 루트를 통해서 간접적으로 다른 엔티티나 밸류에 접근가능
#### 리포지터리
- 애그리거트 단위로 저장하고 조회한다.
- 응용서비스는 의존 주입과 같은 방식으로 리포지터리 구현 객체를 주입
```java
@Configuration
public class OrderServiceConfig {
    @Autowired
    private OrderRepository orderRepository;

    @Bean
    public CancelOrderService cancelOrderService() {
        return new CancelOrderService(orderRepository);
    }   
}

@Configuration
public class RepositoryConfig {
    @Bean
    public JpaOrderRepository orderRepository() {
        return new JpaOrderRepository();
    }

    @Bean
    public LocalContainerEntityManagerFactoryBean emf() {

    }
}
``` 
### 요청 처리 흐름
- 표현영역은 사용자 데이터 형식이 올바른지 검사하고 문제 없으면 응용서비스 데이터 포맷에 맞게 변환 후 응용서비스로 기능 실행 위임 (Controller)
- 응용서비스는 모메인 모델을 이용해 구현, 트랜젝션 관리 

### 인프라스트럭처 개요
- 표현, 응용, 도메인 영역을 지원
- 영속성 처리, 트랜잭션, SMTP 클라이언트
- 인프라스트럭쳐를 사용할때는 직접 사용하는것 보다 해당 영역에서 인터페이스를 정의해서 사용하는것이 더 유연한 시스템을 만든다.

### 모듈 구성
- 계층별 패키지 구성 (ui, application, domain, infrastructure)
- 도메인별 계층 구조 
    - (catalog.ui, catalog.application, catalog.domain, catalog.infrastructure)
    - (order.ui, order.application, order.domain, order.infrastructure)
    - (member.ui, member.application, member.domain, member.infrastructure)

