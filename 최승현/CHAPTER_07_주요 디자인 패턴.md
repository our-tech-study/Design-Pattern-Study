# 디자인 패턴이란?

객체 지향 설계를 하다 보면, 비슷한 상황에서 사용했던 설계를 재사용하는 경우가 종종 있다.  
이런 **설계는 특정 상황에 맞는 해결책을 빠르게 찾을 수 있도록 도와주는데, 이렇게 반복적으로 사용되는 설계는 클래스, 객체의 구성, 객체 간 메시지 흐름에서 일정 패턴을 갖는다.**

## 디자인 패턴 습득 시 이점

- 상황에 맞는 올바른 설계를 더 빠르게 적용할 수 있다.
- 각 패턴의 장단점을 통해서 설계를 선택하는데 도움을 얻을 수 있다.
- 설계 패턴에 이름을 붙임으로써 시스템의 문서화, 이해, 유지 보수에 도움을 얻을 수 있다.

# 전략(Strategy) 패턴

- 정의
  - 특정 콘텍스트에서 알고리즘을 별도로 분리하는 설계 방법
  - 콘텍스트는 사용할 전략을 직접 선택하지 않고 DI를 이용해서 전략을 주입 받는다.
  - 전략이 어떤 메서드를 제공할 지의 여부는 콘텍스트가 전략을 어떤 식으로 사용하느냐에 따라 달라진다.
- 사용시점

  - if-else로 구서된 코드 블록이 비슷한 기능을 수행하는 경우
  - 성능의 장단점에 따라 알고리즘을 선택해야 하는 경우

- 사용효과

  - 컨텍스트 외부에서 전략을 쉽게 변경할 수 있다.

- 패턴 적용 대상

**문제점**  
서로 다른 계산 정책들이 한 코드에 섞여 있어, 정책이 추가될 수록 코드 분석을 어렵게 만든다.

```java
public class Calculator {
    public int calculate (boolean firstGuest, List<Item> items) {
        int sum = 0;
        for (Item item : items) {
            if (firstGuest) {
                sum += (int) (item.getPrice() * 0.9) // 첫 손님 10% 할인
            } else if (!item.isFresh()) {
                sum += (int) (item.getPrice() * 0.8) // 덜 신선한 것 20% 할인
            } else {
                sum += item.getPrice();
            }
        }

        return sum;
    }
}
```

- 패턴 적용  
  <img alt="전략패턴" src="./asset/전략패턴.png" width="700px">

```java
public class Calculator {
    public DiscountStrategy discountStrategy;

    public Calculator(DiscountStrategy discountStrategy) {
        this.discountStrategy = discountStrategy;
    }

    public int calculate (List<Item> items) {
        int sum = 0;
        for (Item item : items) {
            sum += discountStrategy.getDiscountPrice(item);
        }

        return sum;
    }
}
```

# 템플릿 메서드(Template Method) 패턴

1. 정의

   - 상위 클래스에서 알고리즘의 골격(동작 방식 구조)을 정하고 알고리즘의 여러 단계 중 일부는 하위클래스에서 재정의하는 패턴

2. 구성요소
   - AbstractClass
     - 템플릿 메서드를 정의한다.
     - 하위 클래스에서 사용된 추상 메서드를 선언한다.
     - 템플릿 메서드는 기능을 구현할 때 추상 메서드를 호출하여 사용한다.
   - ConcreteClass
     - AbstractClass에서 선언되어 있는 추상 메서드를 구현한다.
3. 사용시점
   - 여러 단계를 가진 알고리즘의 골격이 존재한다.
   - 알고리즘의 일부 특정단계에서 상황별로 차이가 난다.
   - 전체적으로 동일하면서 부분적으로는 다른 구문으로 구성된 코드 블록이 중복된다.
   - 일정한 절차를 가지고 함수의 몸체가 크다.
   - 메서드의 몸체가 크고 일정한 절차를 가진 알고리즘의 골격이 존재한다.
   - 하위클래스에 상위클래스의 메서드와 유사한 메서드가 존재한다.
   - 하위클래스에 상위클래스의 메서드로 구성된 메서드가 존재한다.
4. 사용호과
   - 비슷한 형태의 애플리케이션 제작에 필요한 알고리즘 골격을 일반화하여 재사용할 수 있다.
   - 차이가 나는 부분만 재정의함으로써 코드 중복 문제를 해결할 수 있다.
5. 사용예
   - 프레임워크의 전체 알고리즘을 만들 때 사용된다.
   - 애플리케이션의 뼈대를 만드는 작업에서 많이 사용된다.

전략 패턴과 조합하여 사용할 경우 상속을 기반에 두지 않고 조립/위임을 사용하여 런타임에 템플릿 메서드에서 사용할 객체를 교체할 수 있는 장점을 갖게 된다.

# 상태(State) 패턴

1. 정의

- 객체의 내부 상태에 따라 변경되는 행동을 다루는 패턴

2. 구성요소

- Context
  - 사용자가 관심 있는 인터페이스를 정의한다.
  - 객체의 각 상태를 정의한 State의 구현체 인스턴스를 관리한다.
- State
  - Context의 각 상태별 행동을 정의한다.

2. 클래스 다이어그램
   - <img alt="상태패턴" src="./asset/상태패턴.png" width="900px">
3. 사용시점
   - 객체의 행동에 영향을 미치는 상태가 존재
   - 상태에 따른 행위를 처리하기 위해 switch 또는 if-else를 복잡하게 사용하는 경우
4. 사용효과
   - 각 상태의 행동을 별개의 클래스 분리 - 코드를 수정하거나 이해하기가 쉬워짐
   - 관리하기 힘든 if 선언문들을 없앰
   - 특정 형식의 상태 추가나 삭제를 빠르고 유연하게 대처할 수 있게 됩니다.
   - 새로운 상태가 추가되더라도 컨텍스트 코드가 받는 영향은 최소화된다.
   - 상태에 따른 동작을 구현한 코드가 각 상태 별로 구분되기 때문에 상태별 동작을 수정하기 쉽다.

### 상태 패턴 예시

요구 사항
| 동작 | 조건 | 실행 | 결과 |
| ---------------- | ------ | --------- | ------- |
| 동전을 넣음 | 동전 없음이면 | 금액을 증가 | 제품 선택 가능 |
| 동전을 넣음 | 제품 선택 가능이면 | 금액을 증가 | 제품 선택 가능 |
| 제품 선택 | 동전 없음이면 | 아무 동작 하지 않음 | 동전 없음 유지 |
| 제품 선택 | 제품 선택 가능이면 | 제품 주고 잔액 감소 | 잔액 있으면 제품 선택 가능 / 잔액 없으면 동전 없음 |

```java
public class VendingMachine {
  public static enum State { NOCOIN, SELECTABLE } // SOLDOUT

  private State state = State.NOCOIN;

  public void insertCoin(int coin) {
    switch(state) {
      case NOCOIN:
        increaseCoin(coin);
        state = State.SELECTABLE;
        break;
      case SELECTABLE:
        increaseCoin(coin);
    }
  }

  public void select (int productId) {
    switch(state) {
      case NOCOIN:
        break;
      case SELECTABLE:
        provideProduct(productId);
        decreaseCoin();
        if (hasNoCoin()) {
          state = State.NOCOIN;
        }
    }
  }

  // increaseCoin, provideProduct, decreaseCoin 구현
}
```

**문제점**  
상태 증가에 따라 조건문이 여러 코드에 중복 추가되어 코드가 복잡해 진다.  
이는 유지보수 어려움으로 이어진다.

상태에 따라 다르게 동작해야 할 때 상태 패턴을 활용할 수 있다.

**상태 패턴 적용**

```java
public class VendingMachine {
private State state;

public VendingMachine () {
  state = new NoCoinState();
}

public void insertCoin(int coin) {
  state.increaseCoin(coin, this); // 상태 객체에 위임
}

public void select (int productId) {
  state.select(productId, this) // 상태 객체에 위임
}

public void changeState(State newState) {
  this.state = newState;
}

// 기타 다른 기능
}

public class NoCoinState implements State {
@Override
public void increaseCoin(int coin, VendingMachine vm) {
  vm.increaseCoin(coin);
  vm.changeState(new SelectableState());
}

@Override
public void select(int productId, VendingMachine vm) {
  SoundUtil.beep();
}
}

public class SelectableState implements State {
@Override
public void increaseCoin(int coin, VendingMachine vm) {
  vm.increaseCoin(coin);
}

@Override
public void select(int productId, VendingMachine vm) {
  vm.provideProduct(productId);
  vm.decreaseCoin();

  if (vm.hasNoCoin()) {
    vm.changeState(new NoCoinState());
  }
}
}
```

### 이점

OCP 원칙이 잘 적용되어 VendingMachine은 변경에 닫혀 있고 상태 확장에는 열려있다.  
또한, 상태에 대한 동작 수정 시 해당 상태를 처리하는 클래스만 수정할 수 있다.

### 상태 변경은 누가?

**콘텍스트**

- 상태 변경 규칙을 한번에 확인
- 상태 개수가 적고 변경 규칙이 거의 바뀌지 않는 경우

만일, 상태 변경이 자주 일어난다면 상태 변경 처리 코드가 복잡해질 가능성이 높다.

```java
 public class VendingMachine {
  private State state;

  public VendingMachine () {
    state = new NoCoinState();
  }

  public void insertCoin(int coin) {
    state.increaseCoin(coin, this);
    if (hasCoin()) {
      changeState(new SelectableState());
    }
  }

  public void select (int productId) {
    state.select(productId, this)
    if (state.isSelectable() && hasNoCoin()) {
      changeState(new NoCoinState());
    }
  }

  private void changeState(State newState) {
    this.state = newState;
  }

  private boolean hasCoin() {
    // ...
  }

  private boolean hasNoCoin() {
    return !hasCoin();
  }

  // 기타 다른 기능
}
```

**상태 객체**

- 상태 변경 규칙을 각 상태 객체로 분리하고 싶은 경우

상태 변경 규칙이 여러 클래스에 분산되어 있어 규칙을 파악하기 어렵다.  
한 상태 클래스에서 다른 상태 클래스에 대한 의존도 발생

## State 패턴과 Strategy 패턴의 차이점

- State 패턴은 상태를 가지는 객체가 내부적으로 상태에 따라 로직을 분리해서 사용한다.
- Strategy 패턴은 클라이언트가 객체의 행위(전략)를 변경할 수 있다.

# 데코레이터(Decorator) 패턴

1. 정의
   - 기본 기능을 하는 객체와 다양한 부가 기능을 하는 데코레이터들을 조합하여 기능을 확장할 수 있는 패턴
2. 클래스 다이어그램
   - <img alt="데코레이터패턴" src="./asset/데코레이터패턴.png" width="900px">
3. 구성요소
   - Component
     - 기본 기능을 뜻하는 ConcreteComponent와 부가 기능을 뜻하는 Decorator의 공통 기능을 정의한다.
   - ConcreteComponent
     - Component의 인터페이스를 구현한다.
     - 기본 기능을 한다.
     - 여러 부가기능으로 장식을 할 수 있다.
   - Decorator
     - Component와 동일한 인터페이스를 가진다.
     - 장식할 대상이 되는 Component 역할도 한다.
     - 자신이 장식하고 있는 Component 객체를 인스턴스 변수로 가지고 있다.
     - 다양한 부가기능을 나타내는 구체적인 Decorator의 공통 기능(인터페이스)을 제공한다.
   - ConcreteDecorator
     - Decorator의 인터페이스를 구현한다.
     - 기본 기능에 다양한 부가기능을 조합하여 추가할 수 있다.
4. 사용시점
   - 기본 기능과 다양한 부가기능이 존재한다.
   - 부가기능은 기본기능을 활용한다.
   - 부가기능은 여러가지 방식으로 조합이 가능하다.
5. 사용효과
   - 많은 데코레이터를 생성할 수 있다는 것이다.
   - 실시간으로 특정한 객체에 다양한 행동을 부여할 수 있게 한다.
   - 상속을 이용할 경우 다양한 조합의 기능 확장 제공 시 불필요한 클래스 증가로 이어지는데 상속을 사용하지 않으므로 해당 문제를 해결 할 수 있다.

```Java
public abstract class Decorator implements FileOut {
  private FileOut delegate;
  public Decorator(FileOut delegate) {
    this.delegate = delegate;
  }

  protected void doDelegate(byte[] data) {
    delegate.write(data);
  }
}

public class EncryptionOut extends Decorator {
  public EncryptionOut(FileOut delegate) {
    super(delegate);
  }

  public void write(byte[] data) {
    byte[] encryptedData = encrypt(data);
    super.doDelegate(encryptedData)
  }

  private byte[] encrypt(byte[] data) {
    //...
  }
}
FileOut delegate = new FileOutImpl();
FileOut fileOut = new EncryptionOut(new ZipOut(delegate));
fileOut.write(data);
```

# 프록시(Proxy)

1. 정의
   - 어떤 객체의 접근을 제어하는 대리 객체를 제공하여 다양한 선행처리(접근제한, 캐시이용 등)를 하는 패턴
2. 클래스다이어그램
   - ![프록시 패턴 클래스다이어그램](./asset/proxy_pattern_uml.png)
3. 구성요소
   - Proxy
     - Proxy에는 RealSubject 객체에 대한 레퍼런스가 들어 있다.
     - 클라이언트의 요구를 처리할 수 있으면 본인이 처리하고 처리할 수 없다면 RealSubject에게 위임하여 처리한다.
   - RealSubject
     - 실제 작업을 대부분 처리하는 객체이다.
   - Client
     - Proxy를 이용한다.
4. 사용시점
   - 시스템을 기동하는데 있어서 중요한 역할을 하거나 중대한 영향을 미치는 객체가 존재한다.
   - 그 객체에 보안이나 성능향상을 위해 접근 제어를 할 필요가 있다.
5. 사용효과
   - 캐시를 활용하여 성능을 향상시킬 수 있다.
   - 접근을 제어하여 보안이 중요한 객체를 보호할 수 있다.

# 어댑터(Adapter)

## 예시

- 한국 전자기기는 220V로 나옴 만약 해당 전자기기를 일본에서 쓰고 싶지만 일본은 110V만 사용 가능  
  그렇다면 220V 전자기기를 사용하고 싶으니 일본 표준 전압을 220V로 바꿔달라고 할 수 있을까??  
  불가능하다.  
  그래서 돼지코 어댑터 처럼 220V -> 110V로 변환해서 사용

1. 정의

- 재사용하려는 클래스가 제공하는 인터페이스와 클라이언트가 사용하는 인터페이스가 다를 경우가 있는 데 어댑터 클래스를 이용하여 인터페이스를 일치시키는 패턴

2. 클래스 다이어그램
   - ![어댑터 패턴 클래스다이어그램](./asset/adapter_pattern_uml.png)
3. 구성요소
   - Target
     - 클라이언트에게 필요한 메서드(request())를 제공한다.
   - Client
     - Target에서 제공하는 메서드를 사용한다.
   - Adaptee
     - 클라이언트가 원하는 기능을 구현하는 메서드를 가지고 있다.
   - Adapter
     - Adaptee가 제공하는 메서드를 사용하고 Target 인터페이스를 구현한다.
4. 사용시점
   - 다른 개발자가 작성한 클래스를 재사용해야 한다.
   - 클라이언트가 요구하는 인터페이스와 재사용하려는 클래스의 인터페이스가 일치하지 않는다.
   - 애플리케이션에 필요한 기능을 이미 구현한 기존의 클래스가 있는데 수정이 불가능하거나 수정을 하였을 경우에 버그가 발생할 가능성이 높다.
5. 사용효과
   - 인터페이스가 호환되지 않아 쓸 수 없었던 클래스들을 사용할 수 있게 해 준다.
   - 기존 클래스가 제공하는 기능의 안전성을 그대로 유지할 수 있다.
   - OCP 원칙을 준수

## 어댑터 패턴 적용 예시

<img alt="어댑터패턴예시" src="./asset/어댑터패턴예시.png" width="800px">

```typescript
abstract class Fighter {
  abstract attack(): void
  abstract defend(): void
  abstract escape(): void
}

class Warrior extends Fighter {
  attack() {
    console.log("칼을 휘두르기")
  }

  defend() {
    console.log("방패 들기")
  }

  escape() {
    console.log("도망")
  }
}

class WizardAdapter extends Fighter {
  constructor(wizard: Wizard) {
    this.wizard = wizard
  }
  attack() {
    this.wizard.shot_fire_ball()
  }

  defend() {
    this.wizard.shield()
  }

  escape() {
    this.wizard.portal()
  }
}

class Wizard {
  shot_fire_ball() {
    console.log("파이어볼 발사")
  }

  shield() {
    console.log("보호막 씌우기")
  }

  portal() {
    console.log("포탈 열고 이동")
  }
}

const fighter = getFighter(someCondition)
fighter.attack()
fighter.defend()
fighter.escape()
```
