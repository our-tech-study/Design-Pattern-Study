# 단일 책임 원칙(Single Responsibility Principle) - SRP

## 정의

클래스는 단 한 개의 책임을 가져야 한다.

- 한 개의 책임이란 변경될 이유가 하나라는 것

그렇다면 변경될 이유는 누가 정하는 것인가?
이 객체를 사용하는 액터에 의해서 정해진다.

객체에 해당 변경을 요청하는 사용자들의 집단을 액터라고 한다.  
ex) 개발 기획 운영 조직이 있을 때 각 역할이 액터라고 할 수 있다. 이 액터는 다른 요구사항과 기대치를 갖고 있다.

## 책임이란 변경에 대한 것

만약에, 한 클래스가 서로 다른 이유로 변경된다면 여러 책임을 가지고 있다는 것을 의미 한다.

<img alt="단일책임원칙액터" src="./asset/단일책임원칙액터.png" width="500px">

## 단일 책임 원칙 위반 시 문제점

- 유지보수의 어려움
- 재사용의 어려움

1. 유지보수의 어려움

```typescript
class DataViewer {
    constructor(private url: string) {}

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
    constructor(private server: string, private port: string) {

    public display(): void { // 영향
        const data: string[] = loadHtml();
        updateGui(data);
    }

    public loadHtml(): string[] { // 영향
        const client: SocketClient = new SocketClient();
        client.connect(server, port);
        return client.read();
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

2. 재사용의 어려움

- Viewer 기능이 필요한 ViewerRequiredClient 클래스를 만들었을 때 DataViewer를 의존하게 되면 필요 없는 HttpClient 파일까지 필요하다. -> 불필요한 번들 파일의 용량 증가
- ViewerRequiredClient에서 DataViewer를 사용하기 위해 불필요한 생성자 정보를 넣어줘야함

  - 유지보수 저하 (코드 가독성 저하 / 원하는대로 동작하는지 확인하기 어려움)

  <img alt="재사용의어려움" src="./asset/재사용의어려움.png" width="500px">

## 책임은 어떻게 부여할 수 있는가?

Top -> Down 방식으로 초기 설계부터 책임을 부여할 수 있는가?  
액터 -> 패키지 다이어그램 -> 클래스 다이어그램 -> 구현

초기 설계에 책임을 분류 할 수 있으면 좋겠지만 구현의 한계, 요구 사항의 변경 등으로 인해 설계 초기에 책임을 적절히 분리하는것은 쉽지 않다.

따라서, Bottom -> UP 방식으로 진행될 수 있다.  
구현 -> 리팩토링 -> 액터 구별의 반복

## 단일 책임 원칙을 확인하는 방법

클래스 사용자들이 클래스에 기대하는 역할이 각각 다르다면 해당 클래스에 서로 다른 책임이 부여되었을 가능성이 높다.  
따라서, 책임 분리 후보가 될 수 있다.

ex) GUIApplication은 DataViewer에게 보여주는 역할을 기대한다. / DataProcessor는 DataViewer에게 데이터를 가져오는 역할을 기대한다.

ref:

- https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html
- https://youtube.com/watch?v=AdANHDp5dTM&feature=share

---

# SOLID 정리

SOLID 원칙 - 변화에 유연하게 대처할 수 있는 설계 원칙

### 단일 책임 원칙과 인터페이스분리 원칙

객체가 커지지 않도록 막아줌  
<br>
단일책임 : 강아지 - 먹다 , 짖다 , 걷다 // 프린트 , 로그  
인터페이스분리 : 자신이 사용하는 메서드만 의존 CarBoat , 쓰기/목록/삭제 -> 인터페이스 분리

### 리스코프 치환 원칙과 의존 역전 원칙은 개방 폐쇄 원칙을 지원

개방 폐쇄 원칙 : 확장 열려있고 변경에는 닫혀있다 - 추상화, 다형성 // ex.) 동물 : 강아지 , 고양이(흰고양이, 검은고양이) - 짖다

#### 추상화 - 의존 역전 원칙(저수준 모듈 의존 X, 고수준 모듈에서 정의한 추상화 의존)

동물 - 짖다  
강아지 - 짖다  
고양이 - 짖다  
... 동물이라면 추가 가능하면서 짖다도 자동으로 가져옴(확장 가능) 변경 불가

#### 다형성 - 리스코프 치환 원칙(상위타입 객체 받는 곳은 하위타입 객체로 받아도 사용 가능)

고양이 - 검은 고양이, 흰고양이 - 짖다(까망냐옹, 흰냐옹) ... 고양이 추가 가능 + 고양이 상위 변경 필요없음(개방 폐쇄)

추상화와 다형성
https://wlaxhrl.tistory.com/78

### SOLID 원칙은 사용자 관점에서의 설계를 지향하고 있다.

이런 원칙들을 지키지 않을 경우?
