


## [미디에이터(Mediator) 패턴]


### 정의

객체지향 개발 방법에서는 행동을 여러 객체에 분산시켜 처리하도록 권장하고 있고 이로 인해 객체 구조는 각 객체 간 많은 연결관계가 생김
행동을 여러 객체로 분할하여 각 객체의 재사용성을 증가시킬 수 있지만 객체 간 상호작용이 증가 될수록 모든 참조 객체를 관리해야되면서 재사용성이 낮아질 수도 있음



![관계구조](https://user-images.githubusercontent.com/67131292/201799932-8f7a5135-544b-40d1-90da-a68486ddcbc9.PNG)

 
 
객체 간 의존이 직접 연결되어 있는 경우 각 객체의 재사용이 어려워지고 특정 기능 동작을 위한 흐름을 변경하려면 관련된 객체들을 모두 각각 변경해줘야하는데 참조하고 있는 객체들이 오히려 하나의 단일 구조 형태를 가지면서 일부를 변경하는 것이 더 어려워짐   <br/>
 
  
  <br/>
  <br/>
  
▶ 중간역할을 하는 미디에이터 객체를 생성해서 미디에이터를 통해서만 각 객체들이 상호작용을 한다면, 객체 간 연결정도가 줄어들어서 기능 확장이나 새로운 객체가 추가 되더라도 기존 클래스를 수정할 필요 없이 미디에이터 클래스의 수정만으로 흐름을 변경할 수 있음   <br/>

 <br/>
 <br/>

### 활용

1. 여러 객체가 복잡한 상호작용을 가지고 있어서 객체간 의존성이 이해되기 어려운 경우
2. 한 객체가 다른 객체를 너무 많이 참조하고 있어서 해당 객체를 재사용하기 어려운 경우
3. 여러 클래스에 분산된 행동이 상속없이 상황에 맞게 수정되어야 하는 경우   

<br/>
<br/>

### 구조

![미디에이터 구조](https://user-images.githubusercontent.com/67131292/201800088-f6bb959f-4794-42c0-b928-5937875bc07a.PNG)
  
  
   
- Mediator ; Colleague 객체와 교류하는데 필요한 인터페이스
- Concrete Mediator : Colleague 객체와 상호작용으로 행동을 구현하고 협력할 Colleague 객체를 파악하고 관리
- Colleague : Colleague 객체의 인터페이스
- Concrete Collegue : 미디에이터 객체를 파악하고 다른 Colleague 객체와 상호작용이 필요하다면 미디에이터 클래스를 통해 통신
<br/>
  
  <br/>
 
미디에이터 패턴 적용 시 객체 간의 동작 흐름이 서로 다른 행동에서 반복적으로 사용되는 경우에 추상 Mediator 클래스나 추상 Colleague 클래스를 사용해서 미디에이터 자체의 재사용성을 증가   <br/>

  
  <br/>
  <br/>

어떻게 상호작용해서 구현할지는 추상클래스를 상속받은 구현 클래스 내에서 흐름을 정의하여 mediator객체를 두고 이를 통해 간접적으로 상호작용하도록 구현    <br/>

<br/>
<br/>


  ``` java
 

public abstract class Mediator {

    protected List<colleague> colleagues;    // 중재할 객체들을 담을 그릇 

    public Mediator() {
        colleagues = new ArrayList&lt;&gt;();
    }

    public boolean addColleague(Colleague colleague){
        
        if(colleagues != null) {
            return colleagues.add(colleague);
        }
        else {
            return false;
        }
    }

    public abstract void mediate(String data);

}


public abstract class Colleague {

    private Mediator mediator;        // 연결되어 있는 Mediator

    public boolean registerMediator(Mediator mediator) {
        if (mediator ==null) {
            return false;
        }

        this.mediator = mediator;
        return mediator.addColleague(this);
    }

    public void sendData(String data) {
        if (mediator != null) {
            mediator.mediate(data);
        }
    }

    abstract public void handle(String data);

}


public class ChatMediator extends Mediator {

    @Override
    public void mediate(String data) {
        for (Colleague colleague : colleagues) {
            colleague.handle(data); // handle 메소드 내 중재할(전달할) 로직 구현 
        }
    }

}


public class ChatColleague extends Colleague {

    @Override
    public void handle(String data) {
        System.out.println(data);
    }

}


public class Application {

    public static void main(String[] args) {
        Mediator mediator = new ChatMediator();

        Colleague colleague1 = new ChatColleague();
        Colleague colleague2= new ChatColleague();
        Colleague colleague3= new ChatColleague();

        colleague1.registerMediator(mediator);
        colleague2.registerMediator(mediator);
        colleague3.registerMediator(mediator);

        colleague1.sendData("AAA");
        colleague2.sendData("BBB");
        colleague3.sendData("CCC");
        
        /*
        
        c들이 어떤 m에 조인되어 있고 데이터 보내면 m가 Mediate해서 전달 하는 형태 
        
        */

    }
}


```
