# Chapter5. 설계 원칙: SOLID

>4. 인터페이스 분리 원칙(Interface Segregation Principle)  
4.1. 인터페이스 변경과 그 영향  
4.2. 인터페이스 분리 원칙  
4.3. 인터페이스 분리 원칙은 클라이언트에 대한 것  

</br>

## 목차

## 개요
SOLID: 객체 지향적으로 설계하는데 기본이 되는 설계 원칙  

SOLID 설계 원칙)  
- 단일 책임 원칙 (Single Responsibility Principle; SRP)  
- 개방-폐쇄 원칙 (Open-Closed Principle; OCP)  
- 리스코프 치환 원칙 (Liskov Substitution Principle; LSP)  
- 인터페이스 분리 원칙 (Interface Segregation Principle; ISP)  
- 의존 역전 원칙 (Dependency Inversion Principle; DIP)  

<br/>
1. 단일 책임 원칙 (SRP)  
: 클래스는 단 한 개의 책임을 가져야 한다.  
<br/>
2. 개방-폐쇄 원칙 (OCP)  
: 확장에는 열려 있어야 하고 변경에는 닫혀 있어야 한다.  
<br/>
3. 리스코프 치환 원칙 (LSP)  
: 상위 타입의 객체를 하위 타입의 객체로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 한다.  
<br/>
4. 인터페이스 분리 원칙 (ISP)  
: 인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 한다.  
<br/>
5. 의존 역전 원칙 (DIP)  
: 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안 된다. 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야 한다.  
<br/>
<h3> 인터페이스 분리 원칙 </h3>
인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 한다.  
-> 클라이언트가 <b>자신이 이용하지 않는 메서드에 의존하지 않아야</b> 한다는 원칙  

<br/>

## 인터페이스 분리 원칙(ISP)을 위반하는 예시
```
public interface Vehicle {
	public void go();
	
	public void fly();
} 


public class Aircraft implements Vehicle {
	public void go(){
		Console.WriteLine("Go");
	}
	
	public void fly(){
		Console.WriteLine("Fly");
	}
}

public class Car implements Vehicle {
	public void go(){
		Console.WriteLine("Go");
	}
	
	public void fly(){
		throw new Exception("can not fly"); // Car 클래스는 Vehicle 클래스를 상속 받기 때문에 반드시 fly를 구현해야 함
	}
}
```

하지만 Car class를 fly를 사용하지 X  
-> ISP 위반  

<h4>인터페이스 분리 원칙 적용</h4>
위의 예시에 인터페이스 분리 원칙을 적용한다면?  
-> Vehicle 인터페이스를 <b>Movable, Flyable</b> 인터페이스로 나누고 Flyable 인터페이스는 Movable 인터페이스를 상속 받도록 함  

```
public interface Movable { 
	public void go();
}

public interface Flyable extends Movable { 
	public void fly();
}


public class Aircraft implements Vehicle {
	public void go(){
		Console.WriteLine("Go");
	}
	
	public void fly(){
		Console.WriteLine("Fly");
	}
}

public class Car implements Movable {
	public void go(){
		Console.WriteLine("Go");
	}
}
```
<br/>
인터페이스 분리 원칙(ISP)은 인터페이스가 응집력 측면에서 작게 분할하여 클래스가 필요한 작업만 실행하도록 하는 것을 목표로 함  
-> 따라서 ISP는 단일 책임 원칙(SRP)과 비슷한 목표를 가지고 있음  

<br/>

## 1. 인터페이스 변경과 그 영향
인터페이스 분리 원칙은 C나 C++와 같이 <b>'컴파일'과 '링크'를 직접 해주는 언어</b>를 사용할 때 장점이 잘 드러남  

ex) 게시글 작성, 게시글 목록, 게시글 삭제 기능을 모두 제공하는 ArticleService 클래스를 구현할 경우  
(헤더 파일인 ArticleService.h 파일에 클래스의 인터페이스 명세가 코딩되고 ArticleService.cpp 파일에는 구현이 코딩될 것  
이 클래스를 사용하는 코드는 #include를 이용해 ArticleService.h 파일에 정의된 타입 정보를 이용해 코드를 작성)  

만약 ArticleService 클래스의 <b>'게시글 목록 읽기'</b> 기능과 관련된 멤버 함수의 시그너처에 변경이 발생된다면?  
-> ArticleService.h 헤더 파일이 변경되었기 때문에 이 헤더 파일을 사용하는 '게시글 작성 UI', '게시글 삭제 UI'의 소스 코드도 다시 컴파일해서 오브젝트 파일 만들어 줘야 함  
즉, <b>게시글 목록 기능</b>과 관련 없는 '게시글 작성', '게시글 삭제' 소스도 다시 컴파일한 것  

## 2. 인터페이스 분리 원칙
<h4>위의 문제점 해결 방법)</h4>
ArticleService 인터페이스를 각 클라이언트가 필요로 하는 인터페이스들로 분리함으로써 각 클라이언트가 사용하지 않는 인터페이스에 변경이 발생하더라도 영향을 받지 않도록 만들어야 함  

**
ArticleListService.h 인터페이스에 변경이 발생하더라도 게시글 목록 UI만 영향을 받을 뿐 게시글 쓰기, 게시글 삭제 UI는 재컴파일 할 필요 X 


인터페이스 분리 원칙이 소스 재컴파일 문제만 관련된 것은 X,  
용도에 맞게 인터페이스를 분리하는 것은 단일 책임 원칙(SRP)과도 연결 됨  
단일 책임 원칙(SRP)에서 봤듯이 하나의 타입에 여러 기능이 섞여 있을 경우 한 기능의 변화로 인해 다른 기능이 영향을 받을 가능성이 높아짐  

=> 인터페이스 분리 원칙(ISP)은 인터페이스가 응집력 측면에서 작게 분할하여 클래스가 필요한 작업만 실행하도록 하는 것을 목표로 함  
따라서 인터페이스 분리 원칙(ISP)은 단일 책임 원칙(SRP)과 비슷한 목표를 가지고 있음  

<h4>인터페이스 분리 원칙(ISP)과 단일 책임 원칙(SRP)의 차이점)</h4> 
단일 책임 원칙(SRP)은 '클래스'와 관련된 반면 인터페이스 분리 원칙(ISP)은 '인터페이스'와 관련되어 있음  

<h4>인터페이스 분리 원칙의 효과)</h4>
1. 관련 없는 소스 파일 재컴파일하지 X  
2. 인터페이스와 콘크리트 클래스의 재사용성 ⬆  
  : 단일 책임 원칙(SRP)이 잘 지켜질 때 인터페이스와 콘크리트 클래스의 재사용 가능성을 높일 수 있으므로 인터페이스 분리 원칙(ISP)은 결국 인터페이스와 콘크리트 클래스의 재사용성을 높여 주는 효과도 가짐  


## 3. 인터페이스 분리 원칙은 클라이언트에 대한 것
의존의 양면성  
: 의존 관계에서 상호 간에 영향을 주는 것  
ex) A가 B를 의존할 경우 B의 변화로 인해 A가 변경되지만, 반대로 A의 요구에 의해 B가 변경될 수도 있음  

의존의 양면성에 따라 ArticleService 인터페이스의 변화가 '게시글 목록 UI'에 영향을 주지만 반대로 '게시글 목록 UI'의 요구로 인해 ArticleService 인터페이스가 변경될 수 있음  
=> 이 말은 즉슨, 인터페이스를 분리하는 기준이 <b>클라이언트</b>가 된다는 것을 뜻함  
각 클라이언트가 사용하는 기능을 중심으로 인터페이스를 분리함으로써, 클라이언트로부터 발생하는 인터페이스 변경의 여파가 다른 클라이언트에 미치는 영향을 최소화할 수 있게 됨  




