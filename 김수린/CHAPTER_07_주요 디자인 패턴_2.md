# Chapter7. 주요 디자인 패턴
</br>
12. 컴포지트 패턴</br>
- 12.1. 컴포지트 패턴 구현의 고려 사항</br>
</br>
</br>

## 1. 컴포지트 패턴이란?

<h3>컴포지트(Composite)</h3>
: OOP에서 하나 이상의 유사한 객체를 구성으로 설계된 객체 <br/>
=> 모두 유사한 기능을 나타냄<br/>
<br/>

<h3>컴포지트(copmosite) 패턴</h3>
: 여러 개의 객체들로 구성된 '복합 객체'와 '단일 객체'를 클라이언트에서 구별 없이 다루게 해주는 패턴<br/>
<br/>
<br/>
ex) 빌딩의 장비들의 전원을 관리하는 제어 프로그램 개발<br/>
-> 개별 장비의 전원을 켜고 끄는 기능을 제공하는 인터페이스 정의<br/>
-> 장비 별로 알맞은 콘크리트 클래스 구현 (Aircon, Light 등)<br/>
-> 개별 장비가 아닌 장비들을 하나로 묶어서 관리하는 클래스 추가<br/>

<br/>

![수린](https://user-images.githubusercontent.com/61374766/203269683-065dc758-168e-4e4c-b956-6b2468d78150.PNG)

```
// 장비들의 전원을 제어하는 컨트롤러
public class PowerController {
	// 개별 장비의 전원을 킴
	public void turnOn(Long deviceId){
		Device device = findDeviceById(Long deviced);
		device.turnOn();
	}
	
	// 장비 그룹의 전원을 킴
	public void turnGroupOn(Long groupId){
		DeviceGroup group = findGroupById(Long groupId);
		group.turnAllOn();
	}
}
```

위 코드의 문제점?<br/>
-> turnGroupOn()가 turnOn()은 개별/그룹 차이를 빼면 동일한 기능<br/>
=> powerController 입장에서 봤을 때 '개별 장비'나 '장비 그룹'의 전원을 켜는 동작은 동일한 동작임에도 불구하고 Deivce와 DeviceGroup을 구분해서 처리해야 함<br/>
=> 전원 켜고/끄는 기능 외에 소비 전력 측정과 같은 새로운 기능이 추가될 경우 PowerController 클래스에는 turnOn()과 turnGroup()처럼 거의 동일한 메서드가 추가 됨<br/>
<br/>
<br/>
거의 동일한 코드가 중복된다는 것은 결국 복잡도를 높여서 코드의 수정이나 확장을 어렵게 만듦<br/>
=> 이런 단점을 해소하기 위해 사용되는 패턴이 바로 '컴포지트(composite) 패턴'<br/>
<br/>

![수린2](https://user-images.githubusercontent.com/61374766/203270946-d2ba0787-3bf7-417b-9089-884fcd35c6a1.PNG)

-> 전체-부분을 구성하는 클래스가 동일 인터페이스를 구현하도록 만듦<br/>
=> 부분(Aircon, Light 등)과 전체(DeviceGroup)를 한 개의 인터페이스로 추상화<br/>
<br/>

## 2. 컴포지트 패턴에서 컴포지트의 책임
1. 컴포넌트 그룹 관리<br/>
2. 컴포지트에 기능 실행 요청 시, 포함하고 있는 컴포넌트들에게 기능 실행 요청을 위임<br/>

```
// 컴포지트
public class DeviceGroup implements Device {
	private List<Device> devices = new ArrayList<Device>();
	
	// 책임 1. 컴포넌트 그룹 관리
	// DeviceGroup이 관리할 Device 객체들의 목록 관리
	public void addDevice(Device d){
		devices.add(d); 
	}
	
	public void removeDevice(Device d){
		devices.remove(d);
	}
	
	
	// 책임 2. 포함하고 있는 컴포넌트들에게 기능 실행 요청 위임
	// DeviceGroup이 관리하고 있는 Device 객체들에게 기능 실행 위임
	public void turnOn() {
		for (Device device: devices){
			device.turnOn();
		}
	}
	
	public void turnOff() {
		for (Device device: devices){
			device.turnOff();
		}
	}
}

```
<br/>

짐깐...🙋‍ 어떤 디자인 패턴의 객체와 유사한 지?<br/>

그렇다면 옵저브 패턴과의 차이점은?<br/>
- 컴포지트 타입은 컴포넌트<br/>
- 알림 받을 옵저브 객체의 인터페이스는 따로 존재<br/>

```
// 컴포지트 패턴을 사용하는 부분
Device device1 = new Aircon();
Device device2 = new Light();

DeviceGroup group = new DeviceGroup();
group.addDevice(device1);
group.addDevice(device2);

group.turnOn(); 
// device1과 device2의 turnOn() 실행 
//=> 각 Aircon과 Light의 turnOn() 실행
```

```
// 장비들의 전원을 제어하는 컨트롤러
public class PowerController {
	// DeviceGroup 클래스는 Device 타입
	// => DeviceGroup 타입을 구분할 필요 X
	// => 하나의 메서드로 개별과 그룹을 관리 가능
	public void turnOn(Long deviceId){
		Device device = findDeviceById(Long deviceId);
		device.turnOn();
	}
	
	// 필요 X
	public void turnGroupOn(Long groupId){
		DeviceGroup group = findDeviceById(Long groupId);
		device.turnAllOn();
	}
}
```
<br/>
DeviceGroup 클래스는 Device 타입이라는 말의 의미? <br/>
-> '컴포지트' 자체도 '컴포넌트'가 됨 <br/>
<br/>
컴포지트의 첫 번째 책임 - '컴포넌트 그룹'을 관리한다.<br/>
=> 컴포지트에 다른 컴포지트를 등록할 수 있음<br/>
<br/>
<br/>

```
// 각 층의 Light 객체를 모은 그룹 생성
DeviceGroup firstFloorLightGroup = new firstFloorLightGroup(light11, light12, light13); //1층의 모든 Light를 포함
DeviceGroup secodFloorLightGroup = new secodFloorLightGroup(light21, light22, light23); //2층의 모든 Light를 포함

// 각 층의 DeviceGroup 객체를 하나의 DeviceGroup에 다시 등록
DeviceGroup allLightGroup = new DeviceGroup();
allLightGroup.add(firstFloorLightGroup);
allLightGroup.add(secodFloorLightGroup);

allLightGroup.turnOff(); //모든 층의 Light 객체의 turnOnff() 실행
```

<br/>

## 3. 컴포지트 패턴의 장점
1. 전체냐 부분이냐, 개별이냐 그룹이냐에 상관 없이 클라이언트는 단일 인터페이스로 기능 실행 가능<br/>
2. 컴포지트 자체도 컴포넌트이기 때문에 컴포지트에 다른 컴포지트를 등록할 수 있음<br/>
<br/>
<br/>

## 4. 컴포지트 패턴 구현 시 고려 사항
1. 컴포넌트를 관리하는 인터페이스를 어디서 구현할 지에 대한 여부<br/>

<br/>
<br>컴포넌트를 관리하는 인터페이스 구현 위치)</b></br>
1. 컴포지트<br/>
<br/>
컴포넌트의 그룹을 관리하는 코드는 컴포지트에 존재<br/>
=> 그룹 장비를 만들어야 하는 코드는 그룹 장비 타입에 '직접' 접근해야 함<br/>
<br/>

```
ex)
DeviceGroup allLightGroup = new DeviceGroup();
allLightGroup.add(firstFloorLightGroup);
```
<br/>
<br/>

그럼 Device 타입에 컴포넌트를 관리하는 인터페이스를 추가하면 <br/>
<b>'클라이언트 입장'</b>에서 DeviceGroup 타입을 사용하지 않고도 그룹 생성할 수 있지 않을까? 🙋‍♀️❓ <br/>

<br/>
<br/>

2. 컴포넌트<br/>


```
ex)
public void addDeviceTo(Device device, Integer toDeviceId){
	Device composite = findDevice(toDeviceId);
	composite.addDevice(device); // Device 타입 사용해서 그룹 생성 가능
}
```
<br/>
그렇게 될 경우..🙄.<br/>
컴포넌트를 상속 받는 컴포지트가 아닌 단말의 입장에선 컴포넌트를 관리하는 코드가 정상 작동하면 ❌<br/>
<br/>
ex) Light 클래스나 Aircon 클래스들은 컴포넌트들을 추가하고 제거하는 컴포넌트를 관리하는 기능이 정상적으로 동작하면 X<br/>
<br/>
<br/>
=> 이런 상황이 발생하지 않도록 컴포넌트에 익셉션을 발생시키는 기본 구현을 추가하고 <br/>
DeviceGroup 클래스에서 알맞게 재정의하도록 구현<br/>
<br/>

```
ex)
public abstract class Device {
	public void addDevice (Device d){
		throw new CanNotAddException("추가할 수 없음"); // 익셉션을 발생 시키는 기본 구현 추가
	}
	
	public void removeDevice(Device d){
		// 빈 기본 구현
	}
	
	public abstract void turnOn();
	public abstract void turnOff();
} 


// DeviceGroup 클래스에서 알맞게 재정의하도록 구현
public class GroupDevice extends Device {
	@Override
	public void addDevice (Device d){
		devices.add(d);
	}
	
	@Override
	public void removeDevice(Device d){
		devices.remove(d);
	}
	...
} 
```
<br/>
<br/>
익셉션을 발생시키는 방법보다 조금 더 나은 방법이 있다면❓<br/>
-> 컴포넌트를 추가할 수 있는지의 여부를 판단 해주는 기능을 Device 타입에 정의<br/>

<br/>

```
ex)
public abstract class Device{

	// 컴포넌트를 추가할 수 있는지의 여부를 판단
	public boolean canContain(Device device){
		return false;
	}
}

// GroupDevice에서 알맞게 재정의
public  class GroupDevice extends Device{
	...
	
	//컴포넌트를 추가할 수 있는지의 여부를 판단
	@Override
	public boolean canContain(Device d){
		return true;
	}
}
```
<br/>

canContarin() 메서드는 <b>파라미터로 전달받은 Device 객체를 추가할 수 있는지에 대한 여부를 판단</b>하는 책임을 가짐<br/>
=> 특정 Device 객체에 다른 Device 객체를 추가할 때에는 canContain() 메서드 이용해 가능<br/>

```
ex)
public void addDeviceTo(Device device, Integer toDeviceId){
	Device composite = findDevice(toDeviceId);
	
	if(composite.canContain(device)){
		composite.addDevice(device);
		return;
	}
	
	... // 추가할 수 없는 경우의 처리
}
```
<br/>
<br/>
- canContain() 메서드는 객체 등록 뿐만 아니라 컴포지트에서 포함할 객체를 제한할 때에도 사용 가능<br/> 


```
ex)
// Aircon 객체만 포함하는 컴포지트 클래스
public class OnlyAirconContainerGroup extends GroupDevice {
	@Override
	public boolean canContain(Device device){
		return (device instanceof Aircon);
	}
}
```
