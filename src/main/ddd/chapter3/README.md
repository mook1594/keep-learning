#### [GO TO BACK](../README.md)

# 3. 애그리거트
> 애그리거트  
> 애그리거트 루트와 역할  
> 애그리거트와 리포지터리  
> ID를 이용한 애그리거트 참조  

### 애그리거트
: 관련된 객체를 하나의 군으로 묶어주는 개념
##### 쇼핑몰 시스템 상위 수준 개념
- 주문
- 상품
- 회원
- 결제
- 상품리뷰

### 애그리거트 루트
- 핵심 역할은 애그리거트의 일관성이 깨지지 않도록 하는 것
#### 도메인 규칙과 일관성
- 주문 애그리거트는 배송지 변경, 상품 변경 과 같은 기능을 제공하는데 루트 애그리거트가 그 기능의 메소드를 제공한다.
```java
public class Order {
	public void changeShippingInfo(ShippingInfo newShippingInfo) {
		verifyNotYetShipped();
		setShippingInfo(newShippingInfo);
    }
    
    private void verifyNotYetShipped() {
		if(state != OrderState.PAYMENT_WAITING && state != OrderState.WAITING) {
			throw new IllegalStateException("already shipped");
		}
    }
    
    private void setShippingInfo(ShippingInfo newShippingInfo) {
		// 밸류가 불변이면, 새로운 객체를 할당해서 값을 변경해야 한다.
        // 불변이므로 this.shippingInfo.setAddress(newShippingInfo.getAddress))와 같은 코드 사용 불가
        this.shippingInfo = newShippingInfo;
    }
}
```
- 애그리거트 루트가 아닌 다른 객체가 애그리거트에 속한 객체를 직접 변경하면 안된다.
- 애거리거트 루트가 강제하는 규칙을 적용할 수 없어 모델의 일관성이 깨지는 원인이 된다.
- 불필요한 중복을 피하고 루트를 통해서만 도메인 로직을 구현하게 만든다.
    - 단순히 필드를 변경하는 set ㅁㅔ서드를 공개(public) 범위로 만들지 않는다.
    - 밸류 타입은 불변으로 구현한다.
#### 애그리거트 루트의 기능 구현
##### 다른 객체 조합
```java
public class Order {
	private Money totalAmounts;
	private List<OrderLine> orderLines;
	
	private void calculateTotalAmounts() {
		// 총 주문 금액을 구하기 위해 orderLine 목록을 사용한다.
		int sum = orderLines.stream()
            .mapToInt(ol -> ol.getPrice() * ol.quantity())
            .sum();
		this.totalAmounts = new Money(sum);
    }
}
```
##### 기능 실행을 위임
```java
// 일류 객체로 클래스 분리
public class OrderLines {
	private List<OrderLine> lines;
	
	public Money getTotalAmounts() { ...구현; }
	public void changeOrderLines(List<OrderLine> newLines) {
		this.lines = newLines;
    }
}

public class Order {
	private OrderLines orderLines;
	
	public void changeOrderLines(List<OrderLine> newLines) {
		orderLines.changeOrderLines(newLines);
		this.totalAmounts = orderLines.getTotalAmounts();
    }
}
```
##### 트랜젝션 범위
- 트랙잭션 범위는 작을 수록 좋다. 범위가 작을수록 좋다
- 트랜잭션 충돌 방지를 위해 테이블 잠금하는데 대상이 많아질 수록 전체 성능 처치량이 감소
- 트랜잭션에서는 한개의 애그리거트만 수정해야함.
- 두개의 애그리거트를 수정해야 할 시 다른 애그리거트를 직접 변경하지 말고 서비스에서 위임하여 두 애그리거트를 수정하도록 해야한다.

### 리포지터리와 애그리거트
- 리포지터리는 애그리거트 단위로 존재 (Order, OrderLines가 물리적으로 다른 테이블이라고 해도 각각의 리포지터리를 만들지 않는다.)

### ID를 이용한 애그리거트 참조
- 애그리거트도 다른 애그리거트를 참조한다.
- ORM에서 연관된 객체를 로딩하는 기능을 제공하므로 쉽게 구현 가능하지만 다음 문제가 따름
  - 편한 탐색 오용 (다른 애그리거트 객체 상태를 변경하면 안됨)
  - 성능에 대한 고민 (lazy, eager 맞게 로딩 전략 필요)
  - 확장 어려움 (성능 부하로 인해 DBMS 분리, 서로 다른 DB를 사용하게 된다면 ORM 사용 불가)
  => ID를 활용해서 다른 애그리거트를 참조한다.
    
#### ID를 이용한 참조와 조회 성능
- 다른 애그리거트를 ID로 참조하면 어려 애그리거트를 읽어야 할 때 조회 속도가 문제될 수 있다.

