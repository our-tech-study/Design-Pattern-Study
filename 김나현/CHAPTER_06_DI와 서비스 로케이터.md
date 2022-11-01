# [서비스 로케이터]


어플리케이션 영역 - 클래스 / 인터페이스 등 기능을 제공하기 위한 구현부

메인 영역 - 어플리케이션이 기능에 맞게 잘 동작하도록 객체 생성 / 의존 관계 설정 등 각 객체들을 연결


## # 정의
 - 메인영역에서 기능을 위해 필요한 객체를 제공하는 역할을 하는(책임을 갖는) 객체로 외부에서 사용할 객체를 주입해주는 DI 방식과 다르게,  필요한 객체를 서비스로케이터를 통해 가져온 뒤 원하는 기능을 실행
 - 개발환경이나 프레임워크의 제약(실행해 주지 않음)으로 DI 패턴을 적용할 수 없는 경우 의존하고 있는 객체를 찾아 사용할 수 있는 방법
 
 
 ## 1. 서비스 로케이터의 구현
  - 서비스 로케이터는 필요한 객체를 제공하는 역할을 하며, 의존 대상이 되는 객체 별로 제공 메서드를 정의해두고 서비스로케이터가 제공하는 메서드를 이용해서 객체를 구현
 - 어플리케이션 영역의 객체에 직접 접근하는 방식이므로 서비스로케이터의 구현은 메인영역이 아닌 어플리케이션 영역에 위치하고, 메인 영역에서는 서비스로케이터가 제공할 객체를 생성하고 해당 객체로 초기화
 
 
 ### #1 객체 등록 방식

  ① 서비스 로케이터 생성자에 사용할 객체 전달 
  
  ② 서비스 로케이터 인스턴스를 지정하고 참조할  static 메소드 제공
  
  
  ``` java
  // 어플리케이션 영역
public class ServiceLocator {
    
    private JobQueue jobQueue;
    private Transcoder transcoder;
    
    // 서비스로케이터가 제공할 객체 종류가 많을 경우, 한 번에 모든 객체를 전달하면 가독성이 떨어짐
    // 각 개체마다 별도의 set메서드 제공하는 방식 사용
    public ServiceLocator(JobQueue jobQueue, Transcoder transcoder) {
        this.jobQueue = jobQueue;
        this.transcoder = transcoder;
    }
    
    //의존 대상이 되는 객체 별로 제공 메소드 구현
    public Transcoder getTranscoder() {
        return transcoder; // 비디오 변환
    }
    public JobQueue getJobQueue() {
        return jobQueue; // 작업 순차적 진행 
    }
    
    private static ServiceLocator instance;
    
    // 서비스 로케이터 접근 메서드 
    // 초기화
    public static void load (ServiceLocator locator) {
        ServiceLocator.instance = locator;
    }
    
    public static ServiceLocator getInstance() {
        return instance;
    }
}

public class Worker {
    
    public void run() {
        ServiceLocator locator = ServiceLocator.getInstance();
        // 서비스로케이터의 메서드를 이용해서 의존객체 구한 뒤 기능 실행
        JobQueue jobQueue = locator.getJobQueue();
        Transcoder transcoder = locator.getTranscoder();
        
        // 의존객체 사용부
    }
}


// 메인영역
public static void main (String[] args) {
    
    // 의존 객체  = 서비스 로케이터가 제공할 객체
    FileJobQueue jobQueue = new  FileJobQueue();
    FfmpegTranscoder transcoder = new  FfmpegTranscoder();
    
    // 메인 영역에서 사용할 ServiceLocator 객체를 제공할 객체로 초기화 
    ServiceLocator locator = new ServiceLocator(jobQueue, transcoder);
    ServiceLocator.load(locator);
    
    // 어플리케이션 코드 실행부
    Worker worker = new Worker;
    .
    .
    .
}

```

 - 장점 : 생성자나 set 메서드를 사용해서 서비스로케이터가 제공할 객체를 등록 후 어플리케이션 영역의 사용 로직에서는  get메서드를 이용해서 의존 객체를 구하기만 하면 되므로 구현이 상대적으로 쉽다. 
 - 단점 : 서비스로케이터의 객체를 등록하는 로직이 노출되어 있으므로 어플리케이션 영역에서 의존객체 변경이 가능
 
 
  ### #2 상속을 통한 구현 방식
  
    ① 추상메서드를 제공하는 상위 타입 구현
    ② 상위타입을 상속받은 하위타입에서 사용할 객체를 설정
    
```java
  
// 어플리케이션 영역
public abstract class ServiceLocator {
    
    private JobQueue jobQueue;
    private Transcoder transcoder;

    // 의존 객체를 구할 수 있는 추상메서드  정의 
    public abstract Transcoder getTranscoder();
    public abstract JobQueue getJobQueue();
    
    // 생성자는 instance에 자기자신 할당 
    protected ServiceLocator() {
        ServiceLocator.instance = this;
    }
    
    private static ServiceLocator instance;
    public static ServiceLocator getInstance() {
        return instance;
    }
}


// 메인영역 
// 의존객체 변경 시 어플리케이션 영역의 코드 수정이 없도록 하기 위해 메인 영역에 위치 
public class MyServiceLocator extends ServiceLocator {
    
    // 의존 객체 
    private FileJobQueue jobQueue;
    private FfmpegTranscoder transcoder;
    
    public MyServiceLocator() {
        super(); // 부모의 생성자 호출 
        this.jobQueue = new FileJobQueue();
        this.transcoder = new FfmpegTranscoder();
    }
    
    @Override
    public Transcoder getTranscoder() {
        return transcoder;
    }
    
    @Override
    public JobQueue getJobQueue() {
        return jobQueue;
    }
}

public static void main (String[] args) {
    
    // 서비스 로케이터 초기화 
    // 상위클래스의 ServiceLocator 생성자가 호출되므로 MyServiceLocator 객체가 리턴
    // MyServiceLocator에서 제공하는 의존객체만 사용 
    new MyServiceLocator();
    
    // 어플리케이션 코드 실행부
    Worker worker = new Worker;
    .
    .
    .
}

```

### #3 제네릭/템플릿을 이용한 구현

예시로 특정 클래스가 의존하는 객체는 JobQueue 뿐이라도 서비스 로케이터에 다른 객체가 구현되어 있다면 사용하지 않는 타입에 대한 의존이 발생
하지만 의존 객체마다 서비스 로케이터를 다르게 작성할 경우 동일한 패턴의 코드가 반복될 수 있음

```java
// 어플리케이션 영역
public abstract class ServiceLocator {
    
    private static Map<Class<?>, Object&gt; objectMap = new HashMap<Class<?>, Object&gt;();

    // 의존 대상 타입만 get
    public static <t> T get(Class<t>, cls) {
        return (T) objectMap.get(cls);
    }
    
    // 메인영역에서 객체 등록 시 사용 
    public static void regist(Class<t>, cls, Object obj) {
        objectMap.put(cls, obj)
    }
}

```
서비스 로케이터 자체에는 의존 대상이 되는 객체 타입이 명시되지 않기 때문에 사용하지 않는 객체에 대한 의존이 없고 어느정도 인터페이스 분리 가능 


## 2. 서비스 로케이터의 단점

 - 동일 타입의 객체가 다수 필요한 경우 각 객체별로 제공 메서드 생성 필요
 - 자신이 필요한 타입 뿐 아니라 서비스 로케이터가 제공하는 다른 타입에 대한 의존이 함께 발생

```java

   
 public class ServiceLocator {
    public Transcoder getTranscoder(){}
    public JobQueue getJobQueue(){}
    .
    .
    .
}

 
