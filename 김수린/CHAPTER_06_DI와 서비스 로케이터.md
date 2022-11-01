# Chapter5. DI와 서비스 로케이터
</br>
2. DI를 이용한 의존 객체 사용</br>
- 2.1. 생성자 방식과 설정 메서드 방식</br>
- 2.2. DI와 테스트</br>
- 3.3. 스프링 프레임워크 예</br>
</br>
</br>

## 1. DI를 사용해야 하는 이유

<h3>DI를 왜 사용해야 하는가?</h3>
사용할 객체를 직접 생성할 경우, 콘크리트 클래스에 대한 의존 발생함

<br/>
<br/>

```
public class Worker {
	public void run(){
		JobQueue jobQueue = new FileJobQueue(); // 콘크리트 클래스를 사용
	}
}
```

<h3>의존 역전 원칙</h3>
: 고수준 모듈은 저수준 모듈의 <b>'구현'</b>에 의존해서는 안 된다. 저수준 모듈이 고수준 모듈에서 정의한 <b>'추상 타입'</b>에 의존해야 한다. <br/>
=> 자신보다 변하기 쉬운 것에 의존하지 마라 

<br/>
<br/>

콘크리트 클래스를 직접 사용해서 객체 사용하게 되면 ❔ <br/>
=> <b>의존 역전 원칙 위반</b>하여 결과적으로 <b>확장 폐쇄 원칙 위반</b><br/>
=> SOLID 원칙에 위반하여 유연하지 못한 코드 만들게 됨<br/>

<br/>
<br/>

<h3>메인 영역</h3>
: <b>어플리케이션 영역의 객체</b>를 생성하고, 설정하고, 실행하는 책임을 갖는 영역<br/>
=> 어플리케이션 영역에서 사용할 하위 수준의 모듈을 변경하고 싶다면 메인 영역 수정하면 됨

<br/>
<br/>

<h3>DI(Dependency Injection, 의존성 주입)</h3><br/>
: 메인 영역에서 객체를 연결하기 위해 사용되는 방법으로 <b>필요한 객체를 직접 생성하거나 찾지 않고</b> 외부에서 넣어주는 방식

<br/>
<br/>

## 2. 생성자 방식과 설정 메서드 방식

<h3>DI를 적용하기 위해 의존하는 객체를 전달받을 수 있는 방법)</h3>
1. 생성자 방식<br/>
2. 설정 메서드 방식<br/>

<br/>
<br/>

<h3>1. 생성자 방식</h3>
: 생성자를 통해 의존 객체를 전달받는 방식
<br/>
<br/>

```
public class Worker {
	private JobQueue jobQueue;
	private Transcoder transcoder;
	
	public Worker(JobQueue jobQueue, Transcoder transcoder){ // 생성자를 통해 전달받은 객체를 필드에 보관한 뒤, 메서드에서 사용
		this.jobQueue = jobQueue
		this.transcoder = transcoder;
	}
	
	public void run(){
		while(someRunningCondition){
			JobData jobData = jobQueue.getJob();
			transcoder.transcode(jobData.getSource(), jobData.getTarget());
		}
	}
}

public class JobCLI {
	private JobQueue jobQueue;
	
	public JobCLI (JobQueue jobQueue){ // 생성자를 통해 전달받은 객체를 필드에 보관한 뒤, 메서드에서 사용
		this.jobQueue = jobQueue;
	}
	
	public void intetact(){
		...
		this.jobQueue.addJob(jobData);
	}
}

// 메인 영역은 생성자에 의존 객체를 주입
public class Main {
	public static void main(String[] args){
		JobQueue jobQueue = new JobQueue();
		Transcoder transcoder = new Transcoder();
		
		final Wokrer worker = new Worker(jobQueue, transcoder);
		
		Thread t = new Thread(new Runnable(){
			public void run(){
				wokrer.run();
			}
		});
		
		JobCLI cli = new JobCLI(jobQueue); // 생성자 통해 사용할 객체를 외부에서 직접 주입
		cli.interact();
	}
}
```
<br/>
<br/>
* Worker나 JobCLI 객체는 스스로 의존하는 객체를 찾거나 생성하지 않고 main() 메서드에서 생성자 통해 사용할 객체를 직접 생성하여 주입함 <br/>
외부에서 의존하는 객체를 넣어주기 때문에 이런 방식을 의존 주입(Dependency Injection)이라고 부름 <br/>
<br/>
<br/>

잠깐 👩 .. <br/>
- final 예약어: JAVA에 존재하는 예약어<br/>
final 키워드 사용해 객체를 한 번만 할당할 수 있음<br/>
즉, 객체를 불변값으로 지정 (1번만 초기화 가능)<br/>

<br/>
<br/>
- 필드에 사용할 경우) <br/>
기능: 수정 불가 <br/>

<br/>

```
// 기본형 변수는 값 변경 불가
final int Time = 60;
time = 30; // 컴파일 에러 발생


// 참조형 변수는 객체 내부의 값은 변경 가능하지만 객체는 변경 불가
final Person person = new Person('김씨', 27);
person = new Person('이씨', 28); // 컴파일 에러 발생

person.setName('이씨'); // 가능
person.setAge(28); // 가능
```
<br/>
<br/>

- 메소드에 사용할 경우)<br/>
기능: 메소드 오버라이드 제한<br/>

```
public class parent {
	public void function() {...}
	public final void function2() {...}
}

public class child extend parent {
	public void function() {...}
	public void function2() {...} // 컴파일 에러 발생
}
```

<br/>
<br/>

- 클래스에 사용할 경우)<br/>
기능: final로 선언된 클래스는 다른 클래스에 상속될 수 없음<br/>
=> final 통해 클래스의 상속 제한<br/>

```
public final class finalClass {
	...
}

public class child extend finalClass { // 컴파일 에러 발생
	...
}
```
<br/>

* C#에서 자바의 final과 유사한 예약어) <br/>
변수 - readonly, const<br/>
메서드 - sealed<br/>
클래스 - sealed<br/>

<br/>
<br/>
<h3>2. 설정 메서드 방식</h3>
: 메서드를 이용해 의존 객체 전달받는 방식<br/>

<br/>
<br/>

```
public class Worker {
	private JobQueue jobQueue;
	private Transcoder transcoder;
	
	public void setJobQueue(JobQueue jobQueue){ // 메서드의 파라미터로 전달받은 의존 객체를 필드에 보관
		this.jobQueue = jobQueue;
	}
	
	public void setTransCoder(Transcoder transcoder){
		this.transcoder = transcoder;
	}
	
	public void run() { 
		while(someRunningCondition){
			JobData jobData = jobQueue.getJob(); // 다른 메서드에서는 필드를 사용하여 의존 객체의 기능을 실행
			transcoder.transcode(jobData.getSource(), jobData.getTarget());
		}
	}
}
```
<br/>
<br/>

* 아래와 같은 케이스로도 작성 가능<br/>
또 다른 케이스 1)

```
public void configure(JobQueue jobQueue, Transcoder transcoder){ // 한 개의 메서드로 의존 객체 모두 설정 
	this.JobQueue = jobQueue;
	this.transcoder = transcoder;
}
```
<br/>

또 다른 케이스 2)

```
public Worker setJobQueue(JobQueue jobQueue){ // 메서드 체이닝이 가능하도록 리턴 타입을 void에서 Worker로 변경
		this.jobQueue = jobQueue;
		return this;
	}
	
public Worker setTransCoder(Transcoder transcoder){
	this.transcoder = transcoder;
	return this;
}
```
<br/>
<br/>

잠깐 👩 .. <br/>
<b>메서드 체이닝:</b><br/>
연속적인 코드 줄에서 개체의 메소드를 반복적으로 호출하는 것<br/>
=> 메서드가 객체를 반환하면 그 반환 값(객체)이 또 다른 메서드를 호출할 수 있음<br/>
<br/>

```
Worker worker = new Worker();
worker.setJobQueue(jobQueue);
worker.setTransCoder(transcoder);

Worker worker = new Worker();
worker.setJobQueue(jobQueue).setTransCoder(transcoder); // 메서드 체이닝 적용
```

</br>
</br>

## 3. DI의 각 방식의 장단점

<h3>생성자 방식의 장단점)</h3>
<h4>- 장점</h4>
 - 생성자를 통해 필요한 의존 객체를 전달받기 때문에 객체를 생성하는 시점에서 의존 객체가 정상인지 확인 가능
 <br/>
 ```
public class JobCLI {
	private JobQueue jobQueue;
	
	public JobCLI(JobQueue jobQueue){
		if(jobQueue == null){ //생성 시점에 의존 객체를 모두 받기 때문에 한번 객체가 생성되면 객체가 정상적으로 동작함을 보장 가능
			throw null IllegalArgumentException();
		}
		this.jobQueue = jobQueue;
	}
}
```
<br/>
<br/>

<h4>- 단점</h4> 
 - 생성자 방식을 사용하려면 의존 객체가 먼저 생성되어 있어야 하므로 의존 객체를 먼저 생성할 수 없다면 생성자 방식 사용 불가
<br/>

```
JobCLI jobCli = new JobCLI(null); // 생성 시점에 Exception 발생
jobCli.interact(); // 코드가 실행되지 X
```
<br/>
<br/>

<h3>설정 메서드 방식의 장단점)</h3>
<h4>장점</h4>
 - 객체를 생성한 뒤에 의존 객체 주입 가능
 - 의존할 객체가 많을 경우 메서드 이름을 통해 어떤 의존 객체가 설정되는 지 생성자 방식보다 쉽게 알 수 있음 (코드 가독성 ⬆)

```
Wokrer wokrer = new Worker();
worker.setJobQueue(...); // 메서드 이름으로 의존 객체를 알 수 있음
worker.setTranscoder(...);
worker.setLogSender(...);
worker.setStateListener(...);
```
 
<h4>단점</h4> 
 - 의존 객체를 설정하지 못한 상태에서 객체 사용하면 NullException 발생할 수 있음
<br/>
```
Worker worker = new Worker();
worker.run(); // jobQueue, transcoder가 null이므로 NullException 발생
```
<br/>
<br/>
## 4. DI와 테스트

* DI는 의존 객체를 Mock 객체로 쉽게 대체할 수 있도록 하여 단위 테스트를 할 수 있도록 도움
<br/>

```
// 테스트 코드에서 Mock 객체를 만들어 전달하기 쉬움
@Test
public void shouldRunSuccessfully(){
	JobQueue mockJobQueue = ...;
	Transcoder mockTranscoder = ...;
	
	Wokrer worker = new Worker();
	worker.setJobQueue(mockJobQueue);
	worker.setTranscoder(mockTranscoder);
	worker.run(); // Mock 객체를 이용한 실헹
}
```
<br/>
<br/>

DI를 사용하지 않을 경우?
-> JobQueue의 구현 객체를 구할 수 있는 방법 필요 

```
public abstract class JobQueue{
	public static JobQueue getInstance(){
		return new DbJobQueue();
	}
}

public class Wokrer {
	public void run(){
		JobQueue jobQueue = JobQueue.getInstance();
		while(someRunnngCondition){
			JobData jobData = jobQueue.getJob();
			...
		}
	}
}
```
<br/>
<br/>

만약 DbJobQueue 클래스의 구현이 완성되지 않았다면? <br/>
-> Wokrer 클래스 테스트 하기 위해 JobQueue.getInstance() 메서드가 Mock 객체 리턴하도록 코드 수정 필요함<br/>
=> 한 클래스의 테스트 때문에 다른 클래스의 코드를 변경하는 상황 발생<br/>
=> Mock 객체를 이용한 테스트를 마친 후 다시 원래대로 JobQueue 코드를 되돌려야 함<br/>

<br/>
<br/>
만약 Wokrer 코드의 일부가 변경되어 Worker 클래스를 테스트 하려면 위와 같은 과정 반복 필요함

<br/>
<br/>
<br/>
<br/>

인용)
메서드 체이닝
https://ko.javascript.info/object-methods

final
https://pomo0703.tistory.com/8
https://sabarada.tistory.com/148
