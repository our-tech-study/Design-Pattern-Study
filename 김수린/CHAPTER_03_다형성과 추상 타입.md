<h1>추상화(abstraction)</h1>
구현 변경의 유연함을 얻을 수 있는 방법)
캡슐화
추상화

캡슐화(encapsulatio): 객체가 내부적으로 기능을 어떻게 구현하는 지 감추는 것
-> 내부의 기능 구현이 변경되더라도 그 기능을 사용하는 코드는 영향 받지 않도록 만들어줌

추상화(abstraction): 데이터나 프로세스 등을 의미가 비슷한 개념이나 표현으로 정의하는 과정
추상화를 가능하도록 해주는 방법 -> 다형성

<h1>다형성</h1>  
다형성: 한 객체가 여러가지 모습(타입)을 갖는 것  

다형성 구현 방법)  
인터페이스 상속  
구현 상속  

추상화 예시)  
기능 1. FTP에서 파일 다운로드  
기능 2. 소켓에서 데이터 읽기  
기능 3. DB 테이블의 데이터 조회  

위의 3가지 기능을 분석해보니 모두 로그 수집하기 위한 기능 -> '로그 수집'이라는 프로세스를 처리하는 과정  
즉, 위의 3가지 기능을 추상화 하면 '로그 수집'이라는 개념으로 정의 가능  

class FtpLogFileDownloader {} // 기능 1. FTP에서 파일 다운로드  
class SocketLogReader {} // 기능 2. 소켓에서 데이터 읽기  
class DbTableLogGateWay {} // 기능 3. DB 테이블의 데이터 조회  
-> 각 구현 클래스를 추상화해서 인터페이스 도출 가능  

interface LogCollector { public void collect(); // 추상화 (로그 수집) }  

추상 타입은 구현을 제공할 수 없기 때문에 보통 구현을 제공하지 않는 타입을 이용해서 추상 타입 정의  
ex) 자바의 인터페이스, c++의 추상메서드로만 이루어진 추상 클래스  

주의 - 단순히 구현 클래스로부터 추상 타입을 이끌어 내는 것만이 추상화라고 오해하면 X,  
추상화를 한다고 해서 반드시 추상 타입을 만들어야 하는 것은 아님  
 
<h1>추상화 수준 및 종류</h1>
추상화 수준)
1. 기본적 추상화(basic abstraction)
  : 가장 국지적인(지역적인) '기계 정보'를 수집한 추상화
2. 구조적 추상화(structured abstraction)
  : 보다 전역적인 정보인 '프로그램의 구조'에 대한 추상화
3. 단위 추상화(unit abstraction)
  : '단위 프로그램 전체 정보'에 관한 추상화

추상화 종류)
자료 추상화: 문자열, 수, 트리와 같은 계산될 자료의 특성을 추상화

  - 기본적 추상화 - 컴퓨터 내부의 자료 표현을 추상화
   ex 1) 기억 장치의 위치 -> 변수로 추상화
     int x;
     float y;
   ex 2) 자료의 값(2진수) -> 10진수 (정수, 실수) 자료형으로 추상화
      x = 5.7; // int to float

  구조적 추상화 - 관련된 자료값의 집합을 추상화 (배열, 레코드와 같은 구조형 자료가 전형적인 추상화의 예) ex 1) 형 선언문으로 자료형에 새로운 이름 부여 가능 type student = record ...

  단위 추상화 - 자료의 생성과 사용에 대한 정보를 한 장소에 모아두고 자료의 세부 사항에 대한 접근을 제한하는 도구 ex 1) C++, java의 class ex 2) Ada(에이다)의 package

제어 추상화: 실행 순서를 제어하는 특성을 추상화 (반복문, 조건문, 프로시저 호출 등)

기본적 추상화 - 몇 개의 기계 명령어를 모아 이해하기 쉬운 추상 구문으로 만든 것 ex 1) 계산과 값의 저장을 추상화한 배정문 x := x + y; ex 2) 분기문 if (A.GT.B) GOTO 10

구조적 추상화 - 프로그램에서 어떤 검사된 값에 따라 분할된 명령어의 한 그룹을 수행하도록 하는 것 (검사 값에 따라 분할된 명령어 그룹 수행) (if문, case문, switch문) ex 1) 택일문 if (x > y) then t := x; x := y; y := t; else x := x + y; y := y + 1; endif;

ex 2) 구조화 반복문 (for, repeat, while, loop-exit) i = 0; do i = i + 1; while (sentence[i] = '$');

단위 추상화 - 라이브러리처럼 프로시저의 집합을 추상화 (관련된 프로시저 그룹 추상화) ex 1) C++, java의 class ex 2) Ada(에이다)의 package 등이 라이브러리 형태로 지원

-> 책 p.67 예시)

sum += mark; 추상화 종류)

자료 추상화 - 기본적 추상화 (컴퓨터 내부의 자료 표현을 추상화)
제어 추상화 - 기본적 추상화 (계산과 값의 저장을 추상화)
public class Employee { public void pay() {

} } 추상화 종류)

자료 추상화 - 단위 추상화
추상 타입과 실제 구현 클래스는 상속을 통해 연결됨 상속을 이용해 추상 타입을 실제 구현 클래스로 연결하면 추상 타입을 이용해서 코드 작성 가능 LogCollector collector = createLogCollector() collector.collect();
-> createLogCollector 메서드 내부에서 알맞은 구현 클래스의 객체를 생성한다면 구현 클래스의 collect() 호출됨

콘크리트 클래스(concrete class): 인터페이스에 정의된 기능을 실제로 구현하는 클래스

의문점 - 콘크리트 클래스를 직접 사용해도 문제 없는데 추상 타입을 통해 추상화를 하는 이유? ex) SocketLogReader reader = new SocketLogReader(); reader.collect();
추상화를 사용해야 하는 이유) public class FlowController { // 흐름 제어 객체 public void process(){ FileDataReader reader = new FileDataReader(); byte[] data = reader.read(); // 파일 읽기 객체

	Encryptor encryptor = new Encryptor();
	byte[] encryptorData = encryptor.read(); // 암호화 객체
	
	FileDataWriter writer = new FileDataWriter();
	writer.write(encryptorData); // 파일 쓰기 객체
}
}


요구 사항 1.
파일 외에 소켓을 통해서 데이터를 읽어와 암호화 요청 public class FlowController { // 흐름 제어 객체

private boolead useFile;

public FlowController(boolead useFile){
	this.useFile = useFile;
}

public void process(){

	byte[] data = null;
	if (useFile){
		FileDataReader fileReader = new FileDataReader();
		data = fileReader.read(); // 파일 읽기 객체
	}else{
		SocketDataReader socketReader = new SocketDataReader();
		data = socketReader.read(); // 소켓 데이터 읽기 객체
	}	

	
	Encryptor encryptor = new Encryptor();
	byte[] encryptorData = encryptor.read(); // 암호화 객체
	
	FileDataWriter writer = new FileDataWriter();
	writer.write(encryptorData); // 파일 쓰기 객체
}
}

문제점)

if-else 블록의 코드 구성 비슷
요구 사항이 늘어나면 또 다른 if-else 블록이 process() 내부에 추가될 가능성 ↑
파일을 읽을 수 있는 방법이 늘어나면 boolean 타입인 useFile만으로는 구분 불가
객체는 하나의 책임만 가져야 한다는 단일 책임 원칙 (Single Responsibility Priciple; SRP)을 어긴 형태 -> 파일을 읽어오는 방법을 변경해야 한다면 '파일 읽기' 책임을 가진 객체의 코드만 수정해야 함 FlowController는 파일이건 소켓이건 상관없이 데이터를 읽어 오고 이를 암호화해서 특정 파일에 기록하는 책임만 가져야 함
위의 기능을 추상화 하는 방법)

기존 기능 및 추가 요구 사항 분석

기존 요구 사항: 파일에서
바이트 데이터 읽어와
...
추가 요구 사항: 소켓에서
바이트 데이터 읽어와
... -> 위의 어딘가에서 '데이터를 읽어 온다'는 프로세스가 동일함
추상 타입 생성 public interface ByteSource { public byte[] read(); // 바이트 데이터 읽기를 추상화 한 타입 }

동일한 프로세스를 처리하는 클래스에 추상 타입 상속 받기 public class FileDataReader implements ByteSource { // 파일 읽기 public byte[] read {} }

public class SocketDataReader implements ByteSource { // 소켓 데이터 읽기 public byte[] read {} }

-> 결과) public class FlowController { // 흐름 제어 객체

private boolead useFile;

public FlowController(boolead useFile){
	this.useFile = useFile;
}

public void process(){

	ByteSource source = null;
	
	if (useFile){
		source = new FileDataReader(); // 파일 읽기 객체
	}else{
		source = new SocketDataReader(); // 소켓 데이터 읽기 객체
	}	
	
	byte[] data = sorece.read();
	
	Encryptor encryptor = new Encryptor();
	byte[] encryptorData = encryptor.read(); // 암호화 객체
	
	FileDataWriter writer = new FileDataWriter();
	writer.write(encryptorData); // 파일 쓰기 객체
}
}

문제점)

if-else 블록의 코드 구성 비슷
요구 사항이 늘어나면 또 다른 if-else 블록이 process() 내부에 추가될 가능성 ↑
파일을 읽을 수 있는 방법이 늘어나면 boolean 타입인 useFile만으로는 구분 불가
객체는 하나의 책임만 가져야 한다는 단일 책임 원칙 (Single Responsibility Priciple; SRP)을 어긴 형태 -> 파일을 읽어오는 방법을 변경해야 한다면 '파일 읽기' 책임을 가진 객체의 코드만 수정해야 함 FlowController는 파일이건 소켓이건 상관없이 데이터를 읽어 오고 이를 암호화해서 특정 파일에 기록하는 책임만 가져야 함
=> 소스는 간단해졌지만 해결된 문제점 X

동일한 데이터 타입을 가지는 객체 확인

 ByteSource source = null;
 
 if (useFile){
 	source = new FileDataReader(); // 파일 읽기 객체
 }else{
 	source = new SocketDataReader(); // 소켓 데이터 읽기 객체
 }	
-> '파일 읽기 객체'와 '소켓 데이터 읽기 객체' 모두
ByteSource
타입의 객체

읽기 타입(ByteSource)의 콘크리트 클래스 종류가 변경되더라도 흐름 제어의 객체가 바뀌지 않도록 하는 방법 (흐름 제어 객체가 단일 책임을 갖는 방법)

ByteSource 타입의 객체를 생성하는 기능을 별도 객체로 분리 -> 그 객체를 사용하여 ByteSource 생성
생성자 또는 다른 메서드 이용하여 사용할 ByteSource 전달 받기 (두 번째 방법은 6장 - DI와 서비스 로케이터에서 예시로 나올 예정)
ByteSource 타입의 '객체를 생성하는 기능'을 별도 객체로 분리 방법 - ByteSource 타입의 '객체를 생성해주는 책임을 갖는 또다른 객체'를 만드는 것
동일한 데이터 타입을 가지는 객체를 생성해주는 클래스 구현 public class ByteSourceFactory {

public ByteSource create { if(useFile()) return new FileDataReader(); else return new SocketDataReader(); }

private boolean useFile(){ String useFileVal = System.getProperty('useFile'); return useFileVal != null && Boolean.valueOf(useFileVal); }

// 싱글톤 패턴 적용 - 인스턴스를 한 개로 제한하기 위함 private static ByteSourceFactory instance = new ByteSourceFactory(); public static ByteSourceFactory getInstance(){ return instance; }

private ByteSourceFactory() {} }

-> 결과) public class FlowController { // 흐름 제어 객체

public void process(){

	ByteSource source = ByteSource.getInstance().create();		
	byte[] data = sorece.read(); // 읽기 객체
	
	Encryptor encryptor = new Encryptor();
	byte[] encryptorData = encryptor.read(); // 암호화 객체
	
	FileDataWriter writer = new FileDataWriter();
	writer.write(encryptorData); // 파일 쓰기 객체
}
}

아래 문제점 전부 해소)

if-else 블록의 코드 구성 비슷
요구 사항이 늘어나면 또 다른 if-else 블록이 process() 내부에 추가될 가능성 ↑
파일을 읽을 수 있는 방법이 늘어나면 boolean 타입인 useFile만으로는 구분 불가
객체는 하나의 책임만 가져야 한다는 단일 책임 원칙 (Single Responsibility Priciple; SRP)을 어긴 형태
추상화는 '구현 변경의 유연함'을 얻을 수 있는 방법 중 한 가지 추상화를 통해 얻은 두 가지 유연함)
읽기 객체(ByteSource)의 종류가 변경되면 읽기 객체를 생성하는 객체(ByteSourceFactory)만 변경될 뿐 흐름 제어 객체(FlowController) 클래스는 변경되지 X
흐름 제어 객체(FlowController)의 제어 흐름을 변경할 때 읽기 객체(ByteSource)를 생성하는 부분은 영향 주지 않으면서 흐름 제어 객체(FlowController)만 변경하면 됨
객체는 책임을 작게 가질수록 변경에 대한 유연함을 가질 수 있음

싱글톤(singleton) 패턴: 객체의 인스턴스가 오직 1개만 생성되는 패턴 (최초 한번만 메모리 할당하고 그 메모리에 인스턴스 만들어 사용하는 디자인 패턴)

사용 예시) 객체가 여러 개 생성되면 위험한 타입)

레지스트리 같은 설정 파일 (객체가 여러 개 생성되면 설정 값이 변경될 위험 존재)
장점)

고정된 메모리 영역을 얻으면서 한번의 new 인스턴스를 사용하므로 메모리 낭비 방지
인스턴스가 전역이므로 다른 클래스의 인스턴스들이 데이터를 공유하기 쉬움
인스턴스가 절대적으로 한 개만 존재하는 것을 보증하고 싶을 경우 사용
두 번째 이용시 부터는 객체 로딩 시간이 줄어 성이 좋아짐
단점)

싱글톤 인스턴스가 너무 많은 일을 하거나 많은 데이터를 공유시킬 경우 다른 클래스의 인스턴스들 간에 결합도가 높아져 '개방-폐쇄 원칙'위배함 -> 객체 지향 설계 원칙에 어긋나므로 수정 어려워지고 유지보수 비용 높아짐
멀티쓰레드 환경에서 동기화 처리 안하면 인스턴스 2개 생성될 가능성 존재
=> 싱글톤 패턴은 꼭 필요한 경우 아니라면 지양하기

싱글톤 패턴 방법)

생성자는 외부에서 호출할 수 없도록 private 접근 지정자 사용
자신을 멤버로 선언해서 메모리 올리기
외부에서 instance를 가져올 수 있는 메소드 추가
추상화 설계) 위의 예시에서 암호화 알고리즘이 바뀌거나 데이터를 읽고 쓰는 대상이 바뀌더라도 흐름 제어 객체가 제공하는 상위 수준의 로직은 바뀌지 않고 '재사용' 됨 (로직이 변경되어도 새로운 클래스를 만들 필요 X) -> 재사용의 중요성으로 봤을 때 하위 수준의 상세 구현보다는 변하지 않는 상위 수준의 로직을 재사용할 수 있도록 설계하는 것이 더 중요 재사용 관점 - 상위 수준의 로직 변경 X > 하위 수준의 로직 변경 X

변화될 부분을 미리 예측해서 추상화하는 것은 쉽지 X 쉽게 추상화할 수 있는 방법 - 변화되는 부분을 추상화하기 (요구 사항이 바뀔 때 변화되는 부분은 이후에도 변경될 소지가 많으므로 추상 타입으로 교체 시 향후 변경에 유연하게 대처 가능)
추상화 필요한 코드) 동일 구조를 갖는 if-else 블록 (추상화가 되어 있지 않은 코드는 주로 동일 구조를 갖는 if-else 블록으로 드러남)

추상화 종류 및 수준 관련
https://hcr3066.tistory.com/107
싱글톤 패턴 관련
https://devmoony.tistory.com/43
https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/
