# DI(Dependency Injection)와 서비스 로케이터

Robert C. Martin은 소프트웨어를 두 개의 영역으로 구분  
하나는 고수준 정책 및 저수준 구현을 포함한 엎플리케이션 영역  
다른 하나는 어플리케이션이 동작하도록 각 객체들을 연결해 주는 메인 영역

이 장에서는 메인 영역에서 객체를 연결하기 위해 사용되는 방법인 DI와 서비스 로케이터를 살펴본다.

모든 의존은 메인 영역에서 어플리케이션 영역으로 향한다.

사용할 객체를 제공하는 책임을 갖는 객체를 서비스 로케이터라고 한다.  
이 방법에는 몇 가지 단점이 존재하여 외부에서 사용할 객체를 주입해 주는 DI 방식을 주로 사용한다.

## DI(Dependency Injection)

**DI는 외부에서 의존하는 객체를 넣어주기 때문에 의존(Dependency) 주입(Injection)이라고 한다.**

DI는 두 가지 방식이 있다.

- 생성자 방식 - 생성자를 통해서 전달받은 객체를 필드로 보관  
  객체를 생성하는 시점에 필요한 모든 의존 객체를 준비할 수 있으므로 생성 시점에 의존 객체가 정상인지 확인할 수 있다.

```java
    public class JobCLI {
        private JobQueue jobQueue;

        // 생성자를 통해서 의존 객체를 전달받음
        public JobCLI(JobQueue jobQueue) {
            this.jobQueue = jobQueue;
        }

        public void interact() {
            //...
            this.jobQueue.addJob(jobData)
        }
    }
```

- 설정 메서드 방식 - 메서드를 이용해서 의존 객체를 전달 받는다.  
  객체 생성 이후 의존 객체를 나중에 설정할 수 있다

```java
    public class Worker {
        private JobQueue jobQueue;
        private Transcoder transcoder;

        public void setJobQueue(JobQueue jobQueue) {
            this.jobQueue = jobQueue;
        }

        public void setTranscoder(Transcoder transcoder) {
            this.transcoder = transcoder;
        }

        public void run() {
            while(someRunningCondition) {
                JobData jobData = jobQueue.getJob();
                transcoder.transcode(jobData.getSource(), jobData.getTarget())
            }
        }
    }
```

## 서비스 로케이터를 이용한 의존 객체 사용

서비스 로케이터는 어플리케이션에서 필요로 하는 객체를 제공하는 책임을 갖는다.
DI와 마찬가지로 메인 영역에서 서비스 로케이터가 제공할 객체를 설정한다.  
이후 어플리케이션 영역에서 서비스 로케이터를 직접 접근한다.

```java
// 의존 대상이 되는 객체 별로 제공 메서드를 정의
public class ServiceLocator {
    public Transcoder getTranscoder () {}
    public JobQueue getJobQueue () {}
}

public class Worker {
    public void run() {
        ServiceLocator locator = ...; //
        JobQueue jobQueue = locator.getJobQueue();
        Transcoder transcoder = locator.getTranscoder();

        while(someRunningCondition) {
            JobData jobData = jobQueue.getJob();
            transcoder.transcode(jobData.getSource(), jobData.getTraget());
        }
    }
}
```

**객체 등록 방식의 서비스 로케이터 구현**

- 서비스 로케이터를 생성할 때 사용할 객체 전달
- 서비스 로케이터 인스턴스를 지정하고 참조하기 위한 static 메서드를 제공한다.

```java
public class ServiceLocator {
    private JobQueue jobQueue;
    private Transcoder transcoder;

    public ServiceLocator(JobQueue jobQueue, Transcoder transcoder) {
        this.jobQueue = jobQueue;
        this.transcoder = transcoder;
    }

    public JobQueue getJobQueue() {
        return jobQueue;
    }

    public Transcoder getTranscoder() {
        return transcoder;
    }

    // 서비스 로케이터 접근 위한 static 메서드
    private static ServiceLocator instance;
    public static void load(ServiceLocator locator) {
        ServiceLocator.instance = locator;
    }

    public static ServiceLocator getInstance() {
        return instance;
    }
}

public static void main(String[] args) {
    // 의존 객체 생성
    FileJobQueue jobQueue = new FileJobQueue();
    FfmpegTranscoder transcoder = new FfmpegTranscoder();

    // 서비스 로케이터 초기화
    ServiceLocator locator = new ServiceLocator(jobQueue, transcoder);
    ServiceLocator.load(locator);

    // 어플리케이션 코드 실행
    Worker worker = new Worker();
    JobCLI jobCli = new JobCLI();
    jobCli.interact();
}

public class Worker {
    public void run() {
        // 서비스 로케이터 이용해서 의존 객체 구함
        ServiceLocator locator = ServiceLocator.getInstance();
        JobQueue = jobQueue = locator.getJobQueue();
        Transcoder transcoder = locator.getTranscoder();

        // 의존 객체 사용
        while(someRunningCondition) {
            JobData jobData = jobQueue.getJob();
            transcoder.transcode(jobData.getSource(), jobData.getTarget());
        }
    }
}
```

**상속을 통한 서비스 로케이터 구현**

- 객체를 구하는 추상 메서드를 제공하는 상위 타입 구현
- 상위 타입을 상속받은 하위 타입에서 사용할 객체 설정

```java
public abstract class ServiceLocator {
    public abstract JobQueue getJobQueue();
    public abstract Transcoder getTranscoder();

    protected ServiceLocator() {
        ServiceLocator.instance = this;
    }

    private static ServiceLocator instance;
    public static ServiceLocator getInstance() {
        return instance;
    }
}

public class MyServiceLocator extends ServiceLocator {
    private FileJobQueue jobQueue;
    private FfmpegTranscoder transcoder;

    public MyServiceLocator() {
        super();
        this.jobQueue = new FileJobQueue();
        this.transcoder = new FfmpegTranscoder();
    }

    @Override
    public JobQueue getJobQueue() {
        return jobQueue;
    }

    @Override
    public Transcoder getTranscoder() {
        return transcoder;
    }
}
```

## 서비스 로케이터의 단점

- 동일 타입의 객체가 다수 필요할 경우, 각 객체 별로 제공 메서드를 만들어주어야 한다.
- 인터페이스 분리 원칙을 위배한다. - 제네릭/템플릿을 이용하면 완화할 수 있다.
