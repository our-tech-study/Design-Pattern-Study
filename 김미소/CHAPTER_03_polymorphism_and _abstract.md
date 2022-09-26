# Part01

## Chapter03. 다형성과 추상 타입

### 3.4 인터페이스에 대고 프로그래밍하기

- 개념 : 인터페이스(객체의 기능 집합) 사용하여 프로그래밍함. 자바나 C#에서 interface 및 추상클래스 이용해서 개념적인 인터페이스 제공. 위 규칙을 따를 수 있음.
- 주의 : 인터페이스 사용시 구조 복잡 → 변화 가능헝 높은 경우만 사용.

### 3.5 인터페이스는 인터페이스 사용자 입장에서 만들기

- 개념 : 인터페이스를 사용하는 코드 입장에서 작성. 명칭은 포괄적으로.
- 예시 :
  흐름제어(FlowController)<br>
  FileDataReader(파일리더), SocketFileFataReader(소켓데이터리더)<br>
  → 적절한 명칭은 FileDataReaderIF, \<ByteSource>.

### 3.6 인터페이스와 테스트

- 요점 : concrete class A가 concrete class B를 직접 사용할 때, 개발 및 유지보수 측면에서 불편할 수 있음. (특히 개발 각각 진행하는 경우 테스트 어려움.)
  → '인터페이스' 사용하여 개발 및 유지보수 편하게 가능.

1. 직접 사용 → 테스트 어려움.

```c#
public class FlowController{ //A 개발자 구현
 public void process(){
    FileDataReader r = new FileDataReader(); //B 개발자 구현중
    byte[] data = r.read();
    /// ......
 }
}
```

2. 인터페이스 사용시 → 테스트 가능.

- 인터페이스 사용하고, 실사용 객체(임시로 Mock객체 사용)는 따로 넘겨받음.

```c#
public class FlowController{
   private ByteSource byteSource;
   public FlowController(ByteSource byteSource){
      this.byteSource = byteSource;
   }

 public void process(){
    //FileDataReader r = new FileDataReader(); //직접 생성 안함.

    byte[] data = byteSource.read();
    /// ......
 }
}


// 테스트 코드
public class ProgramTest{
   ByteSource byteSource = new FileDataReader();
   // 실사용 클래스 interface에 할당하여 넘김
   FlowController f = new FlowController(byteSource);
   f.process();
}

// new FileDataReader();  아직 미구현으로 하드코딩으로 값만 강제 할당하여 테스트

class MockByteSource implements ByteSource {
   public byte[] = read(){
      byte[] data = new byte[128];
      // 초기화.
      return data;
   }
}


```

## 참고) <br>

Mockito, jmock : 인터페이스 정의시 Mock 객체 자동 생성
https://javacan.tistory.com/entry/MocktestUsingMockito

## 3.7 테스트 주도 개발 (TDD)

- 개념 : 테스트 코드 작성 → 실제 코드 작성.<br>
  작성한 테스트를 통과하는 코드를 점진적으로 완성해나가는 방식이며, 완성 되지 않은 부분 때문에 테스트 불가한 상황 발생한 경우 인터페이스로 분리하여 Mock 객체 만드는 방식으로 테스트 진행 가능. 인터페이스 사용 과정에서 객체가 알맞은 책임 갖도록 조정됨. (= 객체지향 설계 유도 방식 개발)
