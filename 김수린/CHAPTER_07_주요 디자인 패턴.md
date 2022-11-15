# Chapter7. 주요 디자인 패턴
</br>
8. 옵저버 패턴</br>
- 8.1. 옵저버 객체에게 상태 전달 방법</br>
- 8.2. 옵저버에서 주제 객체 구분</br>
- 8.3. 옵저버 패턴 구현의 고려 사항</br>
</br>
</br>

## 1. 옵저버 패턴이란?

<h3>옵저버(observer) 패턴</h3>
: 한 객체의 상태 변화를 <b>정해지지 않은</b> 여러 다른 객체에 통지하고 싶을 때 사용되는 패턴

<br/>
<br/>

<h3>옵저버 패턴 사용하는 유형)</h3>

// 웹 사이트의 상태를 확인해서 응답 속도가 느리거나 연결이 안 되면 모니터링 담당자에게 이메일로 통지해주는 시스템
```
public class StatusChecker{
	private EmailSender emailSender;
	
	public void check(){
		Status status = loadStatus();
		
		if (status.isNotNormal()){
			emailSender.sendEmail(status);
		}
	}
}
```
<br/>
<br/>

이메일 뿐만 아니라 긴급한 메세지는 SMS로 바로 알려주는 기능을 추가해 달라는 요구가 들어오면? 🤦‍♀️
```
public class StatusChecker{
	private EmailSender emailSender;
	private SmsSender smsSender; // SmsSender 추가
	
	public void check(){
		Status status = loadStatus();
		
		if (status.isNotNormal()){
			emailSender.sendEmail(status);
			smsSender.sendSms(status); // sms 발송
		}
	}
}
```
<br/>
-> 시스템의 문제 상황을 알려주는 방식이 추가될 떄마다, StatusChecker 클래스도 함께 변경됨<br/>
=> 상태가 변경될 때 '정해지지 않은 임의의 객체'에게 변경 사실을 알려주고 싶을 때 '옵저버(Observer) 패턴' 사용 <br/>
=> <b>어떤 객체의 상태</b>가 변할 때 그와 <b>'연관된 객체'들에게 알림</b>을 보내는 디자인 패턴<br/>

<br/>
<br/>


## 2. 옵저버 패턴에서 등장하는 두 가지 객체

<h3>옵저버 패턴에서 등장하는 두 가지 객체)</h3>
1. 주제(subject) 객체 -> 발행자 <br/>
: 본인이 알고 있는 구독자 목록에 '어떤 것'을 '특정 조건 달성 시' 발행함 <br/>

2. 옵저버 (observer) 객체 -> 구독자 <br/>
: 발행자가 발행한 내용을 수신 받음 <br/>

-> 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식<br/>
-> 한 객체(subject)의 상태가 변경되면 그 객체에 의존하고 있는 모든 객체(observers)에게 연락이 감<br/>
=> 일대다 (one-to-many) 의존성 정의<br/>

<br/>
<br/>

<h3>주제 객체의 책임)</h3>
1. 옵저버 목록을 관리<br/>
 -> 옵저버 등록(add)하고 제거(remove)할 수 있는 메서드 제공<br/>
 <br/>
2. 옵저버에게 알림<br/>
 -> 상태의 변경이 발생하면 등록된 옵저버에 변경 내역을 알림<br/>
 <br/>

<h3>주제 클래스 예시)</h3>

```
public abstract class StatusSubject { // 추상 클래스로 구현
	private List<StatusObserver> observers = new ArrayList<StatusObserver>();
	
	// 옵저버 목록 관리하는 책임
	public void add(StatusObserver observer){ // 옵저버 목록에 등록
		observers.add(observer)
	}
	
	public void remove(StatusObserver observer){ // 옵저버 목록에서 삭제
		observers.remove(observer)
	}
	
	// 옵저버에게 변경 내역을 알리는 책임
	public void notifyStatus(Status status){
		for (StatusObserver observer: observers){
			observer.onAbnormalStatus(status); // 옵저버 객체의 메서드를 호출하여 상태에 변화가 생겼음을 옵저버 객체에게 알림
		}
	}
}
```

<h3>주제 클래스의 콘크리스 클래스 예시)</h3>

```
public class StatusChecker extends StatusSubject{ // 주제 클래스 상속
	public void check() {
		Status status = loadStatus();
		
		if(status.isNotNormal()){
			super.notifyStatus(status);
		}
	}
	
	privare Status loadStatus() {
		...
	}
}
```
=> 콘크리트 클래스는 비정상 상태가 감지되면 상위 클래스의 notifyStatus() 메서드를 호출해서 등록된 옵저버 객체들에 상태 값 전달<br/>
=> 옵저버 객체를 구현한 클래스는 주제 객체가 호출하는 메서드에서 필요한 기능 구현하면 됨<br/>
<br/>

<h3>옵저버 인터페이스)</h3>

```
public interface StatusObserver {
	void onAbnormalStatus(Status status);
}
```

<br/>
<br/>
<h3>콘크리트 옵저버 클래스)</h3>

```
public class StatusEmailSender implements StatusObserver{ // 옵저버 인터페이스 상속 

	@Override
	public void onAbnormalStatus(Status status){ // 옵저버 콘크리트 클래스는 옵저버 인터페이스 상속 받아 상태 변화를 통지 받을 때 수행해야 할 기능 구현
		sendEmail(status);
	}
	
	private void sendEmail(Status status){
		... // 이메일 전송 코드
	}
}
```

<br/>
<br/>
* 주제 객체의 상태에 변화가 생길 떄 그 내용을 통지받도록 하려면, 옵저버 객체를 주제 객체에 등록해주어야 함

```
StatusChecker checker = new StatusChecker();
checker.add(new StatusEmailSender()); // 옵저버로 등록
```

<br/>
<br/>

<h3>옵저버 패턴의 장점)</h3>
- 주제 클래스 변경 없이 상태 변경을 통지 받을 옵저버를 추가할 수 있음<br/>
<br/>
이메일 뿐만 아니라 긴급한 메세지는 SMS로 바로 알려주는 기능을 추가해 달라는 요구가 들어오면? 🙋‍♀️

```
checker.add(new FaultStatusSmsSender()); // 옵저버로 등록하기만 하면 끝
```

=> 주제 클래스인 StatusChecker 코드는 바뀌지 X

<br/>
<br/>

## 3. 옵저버 객체에게 상태 전달 방법

* 상황에 따라 '옵저버 객체'가 기능을 수행하기 위해 '주제 객체'의 상태가 필요할 수 있음<br/>
ex) FaultStatusSmsSender 클래스는 장애 상태인 경우에만 SMS 전송 예정<br/>
(응답 속도가 느려진 상태처럼 장애 이외의 비정상 상태인 경우에는 메세지 전송 X)<br/>
-> 이 경우 상태 값 확인 필요<br/>
<br/>

<h3>기존 주제 객체)</h3>

```
public abstract class StatusSubject { 
	private List<StatusObserver> observers = new ArrayList<StatusObserver>();
	
	public void add(StatusObserver observer){ 
		observers.add(observer)
	}
	
	public void remove(StatusObserver observer){ 
		observers.remove(observer)
	}
	
	// 주제 객체가 옵저버 호출 시에 상태 값 전달
	public void notifyStatus(Status status){
		for (StatusObserver observer: observers){
			observer.onAbnormalStatus(status); // 상태를 옵저버에게 전달
		}
	}
}
```

<br/>
<br/>
<h3>기존 콘크리트 옵저버 클래스)</h3>

```
public class FaultStatusSmsSender implements StatusObserver{ 

	@Override
	public void onAbnormalStatus(Status status){ // 옵저버는 파라미터로 전달받은 상태 값을 사용
		if(status.isFault()){
			sendSMS(status);
		}
	}
	
	private void sendSMS(Status status){
		... // SMS 전송 코드
	}
}
```
<br/>
* 경우에 따라 전달 받은 status 객체 만으로 원하는 기능을 구현할 수 없는 경우 존재<br/>
-> 이런 경우에는 옵저버 객체에서 <br>콘크리트 주제 객체에 '직접 접근'</b>하는 방법을 사용함<br/>
<br/>

```
public class SpecialStatusObserver implements StatusObserver {
	private StatusChecker statusChecker; 
	private Siren siren;
	
	public SpecialStausObserver(StatusChecker statusChecker){ // 콘크리트 주제 객체를 의존 주입 통해 의존
		this.statusChecker = statusChecker;
	}
	
	public void onAbnormalStatus(Status status){ 
		if(status.isFault() && statusChecker.isContinuousFault()){ // 옵저버는 파라미터로 전달받은 상태 값 외에도 콘크리트 주제 객체의 메서드 이용
			siren.begin();
		}
	}
}
```
<br/>
<br/>

## 4. 옵저버 패턴이란?옵저버 패턴 구현의 고려 사항

<h3>옵저버 패턴 구현 시 고려 사항)</h3>
1. 주제 객체의 통지 기능 실행 주제<br/>
2. 옵저버 인터페이스의 분리<br/>
3. 통지 시점에서의 주제 객체 상태<br/>
4. 옵저버 객체의 실행 제약 조건<br/>

<br/>
<h2>1. 주제 객체의 통지 기능 실행 주제</h2>
-> 옵저버에 통지하는 시점을 결정하는 주체가 누가 되느냐<br/>
<br/>

```
// StatucChecker가 통지하는 시점 결정
public class StatucChecker exends StatusSubject {
	public void check(){
		Status status = loadStatus();
		
		if(status.isNotNormal()){
			super.notifyStatus(status); 
		}
	}
}
```
<br/>
* 필요에 따라 StatucChecker를 사용하는 코드에서 통지 기능 수행 필요<br/>
ex) 여러 StatucChecker 객체로부터 상태 정보 읽어 와 모두 비정상인 경우에만 통지하고 싶다면?<br/>
<br/>

<h3>주제 객체를 사용하는 코드에서 통지 기능 수행)</h3>

```
StatucChecker checker1 = ...;
StatucChecker checker2 = ...;

checker1.check();
checker2.check();

if(checker1.isLastStausFault() && checker2.isLastStausFault()){
	checker1.notifyStatus(); // StatucChecker를 사용하는 코드에서 통지 기능 수행
	checker2.notifyStatus();
}
```
<br/>
<br/>

<h3>옵저버에 통지하는 시점을 결정하는 주체 정하는 방법)</h3>
1. 주제 객체에서 직접 통지 기능 실행 <br/>
-> 주제 객체의 상태가 바뀔 때마다 옵저버에게 통지 해줘야 하는 경우<br/><br/>
이유 ❔<br/>
주제 객체를 사용하는 코드에서 통지 기능 실행할 경우, <br/>
상태를 변경하는 모든 코드에서 통지 기능을 함께 호출해줘야 하는데 개발자가 통지 기능 호출하지 않는 등의 실수 유발 가능성 존재<br/>
<br/>
<br/>
2. 주제 객체가 아닌 주제 객체의 상태를 변경하는 코드에서 통지 기능 실행  <br/>
-> 한 개 이상의 주제 객체의 연속적인 상태 변경 이후에 옵저버에게 통지해야 하는 경우<br/>
이유 ❔ <br/>
통지 시점 관리 수월<br/>


<br/>
<br/>

<h2>2. 옵저버 인터페이스의 분리</h2>
-> 옵저버 인터페이스 어떤 기능 별로 분리할 것인 지<br/>
<br/>
* 한 주제 객체가 통지할 수 있는 상태 변경 내역의 종류가 다양한 경우<br/>
-> 각 종류 별로 옵저버 인터페이스 분리해서 구현하는 것이 좋음<br/>

```
public interface EventObserver {
	public void onClick(View v); // 마우스 클릭 시 통지
	public void onScroll(View v); // 스크롤 시 통지
	public void onTouch(View v); // 터치 시 통지
}
```
-> 위의 구조는 모든 종류의 상태 변화를 수신하는 옵저버 인터페이스<br/>
=> 해당 옵저버 인터페이스를 상속받는 콘크리트 옵저버 클래스는 모든 메서드 구현 필요<br/>

```
public class OnlyClickObserver implements EventObserver {
	public void onClick(View v){
		... // 이벤트 처리 코드
	}
	
	public void onScroll(View v) {} // 빈 구현
	public void onTouch(View v) {} // 빈 구현
}
```

콘크리트 옵저버 클래스에서 구현할 메서드가 onClick() 뿐이더라도 나머지 메서드의 빈 구현 만들어 줘야 함<br/>
=> 불필요한 코드 만들어야 함<br/>
주제 객체 입장에서도 각 상태마다 변경의 이유가 다르기 때문에 한 개의 옵저버 인터페이스로 관리하는 것은 향후 변경을 어렵게 만드는 요인이 됨<br/>
<br/>
<br/>

<h2>3. 통지 시점에서의 주제 객체 상태</h2>
-> 통지 시점에서 주제 객체의 상태에 결함이 없어야 함<br/>

```
// 주제 객체
public class AnySubject extends SomeSubject{

	private int status;
	
	@Override
	public void changeState(int newValue){
		super.changeState(newValue); // 상위 changeState()에서 옵저버에 통지
		
		// 아래 코드가 실행되기 이전에 옵저버가 상태 조회
		if(stateSome()){
			status += newValue;
		}
	}
}

// 옵저버 객체
public AnyObserver implements SomeObserver{
	private SomeSubject someSubject;
	
	public AnyObserver(SomeSubject someSubject){
		this.someSubject = someSubject;
	}
	
	public void onAbnormalStatus(int newValue){ 
		if(newValue >= SomeSubject.status){ 
			sendMemo();
		}
	}
	
	private void sendMemo(){
		... // SMS 전송 코드
	}
}
```
<br/>
-> 주제 객체는 super.changeState() 통해 옵저버에 통지한 이후 상태 값을 변경함<br/>
=> 통지 받은 시점에서 옵저버 객체는 완전하지 못한 상태 값을 조회하게 되는 것<br/>
<br/>
* 옵저버 객체가 올바르지 않은 상태 값을 사용하게 되는 문제가 발생하지 않도록 만드는 방법 존재 <br/>
-> 상태 변경과 통지 기능에 템플릿 메서드 패턴 적용<br/>

<br/>
<h3>올바른 상태 값 사용하도록 템플릿 메서드 패턴 적용)</h3>

```
// 상위 클래스
public class SomeSubject {
	public void changeState(int newValue){
		internalChangeState(newValue);
		notifyObserver(); // 옵저버에게 상태 변화 통지
	}
	
	protected void internalChangeState(int newValue){
		... 
	}
}

// 하위 클래스
public class AnySubject extends SomeSubject {
	@Override
	protected void internalChangeState(int newValue){
		super.internalChangeState(newValue);
		
		if(isStateSome()){
			state += newValue;
		}
	}
}
```

<br/>
-> 상위 클래스의 changeState() 메서드는 internalChangeState() 메서드 호출한 뒤에 notifyObserver() 메서드 호출해서 옵저버에게 상태 변화 통지<br/>
=> AnySubject 클래스의 internalChangeState() 메서드에서 상태 변화 후 옵저버 객체가 상태 값 접근하게 됨<br/>
=> 옵저버는 완전한 상태 값 사용 가능<br/>

<br/>
<br/>

<h2>4. 옵저버 객체의 실행 제약 조건</h2>
-> 옵저버 객체의 실행에 대한 제약 규칙을 정해야 함<br/>

```
private void notifyToObserver() {
	for (StatusObserver o : observers){
		o.onStatusChange();
	}
}

public void changeState(int newState){
	internalChangeState(newState);
	notifyToObserver();
}
```
	
<br/>
<br/>
만약 10개의 옵저버 객체가 있고, 각 옵저버 객체의 onStatusChange() 메서드마다 실행 시간이 십 분 이상 걸린다면?<br/>
changeState() 메서드 호출한 코드는 모든 옵저버 객체의 onStatusChange() 메서드 실행이 종료될 때까지 100분 이상 기다려야 함...<br/>

<br/>
-> 옵저버 인터페이스 정의 시에 옵저버 메서드의 실행 제한에 대한 명확한 기준 필요<br/>
옵저버 메서드의 실행 제한에 대한 명확한 기준 예시) <br/>
- onStatusChange()는 수 초 이내에 응답 해야 함 <br/>
- 긴 작업 수행해야 할 경우 별도 쓰레드 실행해야 함<br/>
- 옵저버 객체에서 주제 객체의 상태를 다시 변경하면 어떻게 구현할 것인 지 고민해야 함<br/>
- 옵저버 자체를 비동기로 실행할 지 고민해야 함<br/>
... 
<br/>

=> 위의 네 가지 고려 사항들로 옵저버 패턴 적용 시 고민하여 적용하기<br/>
