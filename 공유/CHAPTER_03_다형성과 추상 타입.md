# 상속 개요

기존 클래스에 기능을 추가하거나 재정의하여 새로운 클래스를 정의하는 것

상속하는 클래스는 슈퍼 클래스, 부모 클래스  
상속받는 클래스는 서브 클래스, 자식 클래스라고 부른다.

## 상속의 장점

- 코드의 재사용
  - 부모 클래스에서 이미 속성 및 속성을 처리하는 로직을 다루고 있다면 자식 클래스에서 상속받아 사용할 수 있음
- 코드의 변경 및 확장이 용이
  - 부모 클래스에서 구현한 코드를 변경(오버라이딩)하거나 확장할 수 있음
- 다형성의 문법적 토대를 마련

## 접근 제한자 - TypeScript 기준

| 접근 가능성      | public | protected | private |
| ---------------- | ------ | --------- | ------- |
| 클래스 내부      | O      | O         | O       |
| 자식 클래스 내부 | O      | O         | X       |
| 클래스 인스턴스  | O      | X         | X       |

## 상속 예시

```typescript
class Coupon {
  constructor(private discountAmount: number) {}

  getDiscountAmount() {
    return this.discountAmount
  }

  calculateDiscountedPrice(price: number) {
    if (price < discountAmount) return 0
    return price - discountAmount
  }
}

class LimitPriceCoupon extends Coupon {
  constructor(private limitPrice: number, discountAmount: number) {
    super(discountAmount)
  }

  getLimitPrice() {
    return this.limitPrice
  }

  calculateDiscountedPrice(price: number) {
    // 재정의
    // 정해진 금액을 넘지 않으면 할인이 적용되지 않음
    if (price < this.limitPrice) return price
    return super.calculateDiscountedPrice(price)
  }
}
```

## 추상 클래스(Abstract Class)와 인터페이스(Interface)의 차이

공통점

- 다형성의 문법적 토대를 마련
- 클래스에서 추상 메서드를 구현하도록 강제한다.
- 인스턴스화 할 수 없다.

차이점

- 추상 클래스
  - 상속 가능
  - 일반 메서드, 일반 속성, 생성자 가질 수 있음

사용용도

- 추상 클래스
  - 상속 관계를 통해 동일한 행위의 구현을 재사용하거나 확장하는 경우 사용
- 인터페이스
  - 상속 관계를 넘어서 동일한 행위가 필요한 경우 사용
  - 상속 관계로 정의할 수 없거나 상속을 할 필요가 없는 경우 인터페이스를 통해 동일한 행위를 구현하도록 함

<img alt="추상클래스와인터페이스사용용도" src="./asset/CHAPTER_03/추상클래스와인터페이스사용용도.png" width="700px">

ref: https://myjamong.tistory.com/150