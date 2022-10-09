# 단일 책임 원칙(Single Responsibility Principle) - SRP

## 정의

클래스는 단 한 개의 책임을 가져야 한다.(클래스 변경의 이유는 단 한개여야 한다.)

## 단일 책임 원칙 위반 시 문제점

- 유지보수의 어려움
- 재사용의 어려움

한 개의 책임에 대한 정의가 명확하지 않고, 책임을 도출하기 위해서는 다양한 경험 필요

1. 유지보수의 어려움

```typescript
class DataViewer {
    public display(): void {
        const data: string = loadHtml();
        updateGui(data);
    }

    public loadHtml(): string {
        const client: HttpClient = new HttpClient();
        client.connect(url);
        return client.getResponse();
    }

    private void updateGui (data: string) {
        const guiModel: GuiData = parseDataToGuiData(data);
        tableUI.changeData(guiModel);
    }
    private parseDataToGuiData(data: string): GuiData {
        // ... 처리 로직
    }
}
```

데이터를 읽어 오는 기능이 변함  
HTTP Client -> 소켓 기반의 프로토콜로 변경

```typescript
class DataViewer {
    public display(): void { // 영향
        const data: string[] = loadHtml();
        updateGui(data);
    }

    public loadHtml(): string[] { // 영향
        const client: SocketClient = new SocketClient();
        client.connect(server, port);
        return client.read;
    }

    private void updateGui (data: string[]) { // 영향
        const guiModel: GuiData = parseDataToGuiData(data);
        tableUI.changeData(guiModel);
    }
    private parseDataToGuiData(data: string[]): GuiData { // 영향
        // ... 처리 로직
    }
}
```

위의 예시에서는 두 개의 책임 - 데이터 읽기, 화면에 보여주기가 섞여 있었습니다.  
이렇듯 책임의 개수가 많을 수록 한 책임의 기능 변화가 다른 책임에 주는 영향은 비례해서 증가합니다.  
이는 유지 보수 어려움으로 이어집니다.

## 책임이란 변화에 대한 것

서로 다른 이유로 변경된다면 여러 책임을 가지고 있다는 것
위 예에서 데이터를 읽어 오는 기능이 변경될 때 데이터를 보여주는 기능이 영향을 받는 것은 클래스가 단일 책임 원칙을 어기고 있다고 볼 수 있다.

## 단일 책임 원칙을 확인하는 방법

메서드를 실행하는 것이 누구인지 확인해 보는 것  
클래스의 사용자들이 서로 다른 메서드들을 사용한다면 그들 메서드는 각각 다른 책임에 속할 가능성이 높고 따라서 책임 분리 후보가 될 수 있다.

# 개방 폐쇄 원칙(Open - Closed Principle) - OCP

## 정의

확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 한다.  
다시 말해, 기능을 사용하는 코드는 수정하지 않으면서 기능을 변경하거나 확장할 수 있어야 한다.

## 구현 방법

1. **추상화**

추상화를 이용한 개방 폐쇄 원칙 구현 모습이다.  
추후에 MemoryByteSource라는 기능이 추가 되더라도  
FlowController의 경우 추상화된 ByteSource에 의존하고 있으므로 코드 변경에는 닫혀있다.  
MemoryByteSource는 ByteSource를 새로 구현하면 되므로 기능의 확장에는 열려있다고 할 수 있다.

추상화를 통해 개방 폐쇄 원칙을 구현할 수 있는 이유는 변화되는 부분을 추상화했기 때문이다.  
FlowController에서 잘 변하지 않는 인터페이스를 의존 하므로 관련 기능이 확장되더라도 FlowController는 영향을 받지 않는다
<img alt="개방폐쇄원칙" src="./asset/개방폐쇄원칙.png" width="700px">

2. 상속

```typescript
class ResponseSender {
  constructor(private data: Data) {}

  public send(): void {
    sendHeader()
    sendBody()
  }

  protected sendHeader() {
    // 헤더 데이터 전송
  }

  protected sendBody() {
    // 텍스트로 데이터 전송
  }
}

// ResponseSender는 변경에 닫혀 있다.
// ZippedResponseSender는 확장에 열려 있다.
class ZippedResponseSender extends ResponseSender {
  constructor(data: Data) {
    super(data)
  }

  protected sendBody(): void {
    // 데이터 압축 처리
  }
}
```

## 개방 폐쇄 원칙이 깨질 때의 주요 증상

추상화와 다형성을 이용해서 개방 폐쇄 원칙을 구현한다.  
만일, 추상화와 다형성이 지켜지지 않으면 개방 폐쇄 원칙이 깨진다.

1. 타입 확인 및 특정 타입 특수 처리

```typescript
class Character {
  draw() {}
}

class Player extends Character {
  draw() {}
}

class Missile extends Character {
  draw() {}

  drawSpecific() {}
}

/*
    해당 코드는 추상화와 다형성이 잘 지켜지지 않았다.
    따라서 Missile 코드 변경 시 drawCharacter도 변경되므로 변경에 닫혀있지 않게 된다.
      
    만일, drawSpecific 메서드가 객체마다 변화되는 대상이라면 Character로 올려야 한다.  
*/
function drawCharacter(character: Character) {
  if (character instanceof Missile) {
    // 타입 확인
    missile.drawSpecific() // 별도 처리
  } else {
    character.draw()
  }
}
```

2. 비슷한 if - else 블록이 존재한다.  
   Enemy 캐릭터의 움직이는 경로를 몇 가지 패턴으로 정한다.  
   이때, 정해진 패턴에 따라 경로를 이동하는 코드는 다음과 같다.

```typescript
class Enemy extends Character {
  constructor(private pathPatern: number) {}

  public draw(): void {
    // 새로운 경로 패턴이 추가되면 해당 메서드도 영향을 받는다.
    // 이는 Enemy 클래스가 변경에 닫혀있지 않으므로 개방 폐쇄 원칙이 깨진것이다.
    if (this.pathPattern === 1) {
      x += 4
    } else if (this.pathPattern === 2) {
      y += 10
    } else if (this.pathPattern === 4) {
      x += 4
      y += 10
    }
    // 그리는 코드
  }
}
```

다음과 같은 구조로 개방 폐쇄 원칙을 지킬 수 있다.
<img alt="개방폐쇄원칙2" src="./asset/개방폐쇄원칙2.png" width="700px">

```typescript
class Enemy extends Character {
  constructor(private pathPatern: pathPattern) {}

  public draw(): void {
    const x = this.pathPattern.nextX()
    const y = this.pathPattern.nextY()
    // 그리는 코드
  }
}
```

## 개방 폐쇄 원칙은 유연함에 대한 것

기존 기능을 확장하기 위해 코드를 수정해주어야 한다면, 새로운 기능을 추가하는 것이 점점 힘들어진다.  
확장에는 닫히고 변경에는 열리게 되면서 유지보수가 힘들어진다.

개방 폐쇄 원칙은 변화되는 부분을 추상화해서 사용자 입장에서 변화를 고정시킨다.

상속을 이용하더라도 부모 클래스는 변경에 닫혀있으면서 자식 클래스에서 기능을 확장하도록 한다.
