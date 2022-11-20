# Chapter7. 주요 디자인 패턴

</br>

## 목차
추상 팩토리 패턴   
    1. [추상 팩토리 패턴](#1-추상-팩토리-패턴abstract-factory-pattern)  
    2. [추상 팩토리 패턴 - 샘플](#2-추상-팩토리-패턴---샘플)    

</br>

파사드 패턴  
    1. [파사드 패턴](#1-파사드-패턴facade-pattern)  
    2. [파사드 패턴 - 샘플](#2-파사드-패턴---샘플)     

</br>

# 추상 팩토리 패턴

## 1. 추상 팩토리 패턴(Abstract Factory Pattern)  
- 다양한 구성 요소 별로 `객체의 집합`을 생성해야 할 때 유용한 패턴.  
- 관련성 있는 여러 종류의 객체를 `일관된 방식으로 생성`하는 경우 유용한 패턴.  

<br/>

## 2. 추상 팩토리 패턴 - 샘플  
  
```bash
├── 01 (공통)
│   ├── AbstractFactoryPattern  
│   │   ├── 📁 Bike
│   │   │   ├── 📁Component
│   │   │   │   ├── BikeBody.cs 
│   │   │   │   └── BikeHandle.cs 
│   │   │   ├── 📁Interface
│   │   │   │   ├── IBikeBody.cs 
│   │   │   │   ├── IBikeHandle.cs 
│   └── └── └── └── IBikeFactory.cs
│    
├── 02
│   ├── Bike.GTBike  
│   │   ├── 📁 Component
│   │   │   ├── GtBikeBody.cs 
│   │   │   └── GtBikeHandle.cs 
│   │   └── GtBikeFactory.cs 
│   │
│   ├── Bike.SamchullyBike  
│   │   ├── 📁 Component
│   │   │   ├── SamchullyBikeBody.cs 
│   │   │   └── SamchullyBikeHandle.cs 
│   │   └── SamchullyBikeFactory.cs 
│   │
└── └── Bike.Test  
``` 

</br>

- IBikeFactory   
```c#
public interface IBikeFactory
{
  string Name { get; }
  IBikeBody CreateBikeBody();
  IBikeHandle CreateBikeHandle();
}
```   
- GtBikeFactory   
```c#
public class GtBikeFactory : IBikeFactory 
{
  #region [propeties]
  public string Name => "gt";
  #endregion 
  
  public IBikeBody CreateBikeBody()
  {
    return new GtBikeBody();
  }
  public IBikeHandle CreateBikeHandle()
  {
    return new GtBikeHandle();
  }
}
```   
- SamchullyBikeFactory   
```c#
public class SamchullyBikeFactory : IBikeFactory 
{
  #region [propeties]
  public string Name => "samchully";
  #endregion 
  
  public IBikeBody CreateBikeBody()
  {
    return new SamchullyBikeBody();
  }
  public IBikeHandle CreateBikeHandle()
  {
    return new SamchullyBikeHandle();
  }
}
```   
- BikeManager.cs 
```c#
public class BikeManager
{
  #region [fields]
  private IDictionary<string, IBikeFactory> _bikeFactories;
  #endregion
  
  #region [constructor]
  public BikeManager()
  {
    this._bikeFactories = new Dictionary<string, IBikeFactory>();
  }
  #endregion
  
  public void RegisterFactory(IBikeFactory factory, string factoryName = null)
  {
    if (factory == null) {
      throw new ArgumentNullException("factory cannot be null.");
    }
    
    this._bikeFactories.Add(factoryName ?? factory.Name, factory);
  }
  
  public void GetFactory(string factoryName)
  {
    if (factoryName == null) {
      throw new ArgumentNullException("factoryName cannot be null.");
    }
    
    if (_bikeFactories.TryGetValue(factoryName, out var factory)) {
      return factory;
    }
    
    throw new NotSupportedException($"There is no factory for [{factoryName}].");
  }
}
```   
- UnitTest.cs
```c# 
public class Tests
{
  static BikeManager manager = new BikeManager();
  
  [Setup]
  public void Setup()
  {
    manager.RegisterFactory(new GtBikeFactory(), "gt");
    manager.RegisterFactory(new SamchullyBikeFactory(), "damchully");
  }
  
  [Test]
  public void Test1()
  {
    IBikeFactory factory = manager.GetFactory("gt");
    
    IBikeBody body = factory.CreateBikeBody();
    IBikeHandle handle = factory.CreateBikeHandle();
  }
}
```

</br>
</br>

# Chapter7. 주요 디자인 패턴 - 파사드 패턴

</br>

## 1. 파사드 패턴(Facade Pattern)
- 어떤 기능을 수행하기 위한 여러 객체들 사이의 목잡한 관계/메서드 사용을 `감춰서 단순화`하는 패턴.   
- 여러 객체들 사이의 복잡한 관계를 `블랙박스화해서 단순화`하는 것.  

<br/>

## 2. 파사드 패턴 - 샘플  

- ex. DB에서 데이터를 `조회`해서 `가공` 후 출력하는 기능
```text
1. Cache 조회
2. DB 조회 
3. Cache에 입력
4. 조회 결과 가공
5. 가공된 결과 출력
```   
```bash
├── FacadePattern
│   ├── Row.cs 
│   ├── Db.cs 
│   ├── Cache.cs 
│   ├── Message.cs
│   └── Facade.cs 
│    
└── TestProject
```    
```text
C:\Users\20105seunghui\source\study\FacadePattern\FacadePattern.sln
```

</br>

- Row.cs   
```c# 
public class Row
{
    #region [properties]
    public string Name { get; private set; }
    public string Birthday { get; private set; }
    public string Email { get; private set; }
    #endregion

    #region [constructor]
    public Row(string name, string birthday, string email)
    {
        Name = name;
        Birthday = birthday;
        Email = email;
    }
    #endregion
}
```  
- Db.cs  
```c# 
public class DB
{
    #region [fields]
    private IDictionary<string, Row> _rows = new Dictionary<string, Row>(StringComparer.OrdinalIgnoreCase);
    #endregion

    #region [constructor]
    public DB()
    {
        _rows.Add("seunghui", new Row("seunghui", "1997-04-06", "shwnddow@gmail.com"));
        _rows.Add("jane", new Row("jane", "1979-11-05", "jane09@naver.com"));
        _rows.Add("dorosh", new Row("dorosh", "1985-08-21", "dorosh@haha.com"));
    }
    #endregion

    public bool TryFetch(string name, out Row row)
    {
        return _rows.TryGetValue(name, out row);
    }
}
```   
- Cache.cs   
```c# 
public class Cache
{
    #region [fields]
    private IDictionary<string, Row> _rows = new Dictionary<string, Row>(StringComparer.OrdinalIgnoreCase);
    #endregion

    public void Put(Row row)
    {
        _rows[row.Name] = row;
    }

    public bool TryFetch(string name, out Row row)
    {
        return _rows.TryGetValue(name, out row);
    }
}
```  
- Message.cs   
```c#
public class Message
{
    #region [fields]
    private Row _row;
    #endregion

    #region [constructor]
    public Message(Row row)
    {
        this._row = row;
    }
    #endregion

    public string GetName()
    {
        return $"Name: {_row.Name}";
    }
    public string GetBirthDay()
    {
        return $"Birthday: {_row.Birthday}";
    }
    public string GetEmail()
    {
        return $"Email: {_row.Email}";
    }
}
```  
- UnitTest.cs   
```c# 
public class Tests
{
    private DB _db;
    private Cache _cache;

    [SetUp]
    public void Setup()
    {
        _db = new DB();
        _cache = new Cache();
    }

    [Test]
    public void Test1()
    {
        var name = "dorosh";

        if (!_cache.TryFetch(name, out Row row)) {
            if (!_db.TryFetch(name, out row)) {
                Console.WriteLine($"{name} is not exists");
                return;
            }

            _cache.Put(row);
        }

        var msg = new Message(row);
        Console.WriteLine(msg.GetName());
        Console.WriteLine(msg.GetBirthDay());
        Console.WriteLine(msg.GetEmail());
    }
}
```  

</br>

📝 개발자가 위 기능을 사용하기 위해 알아야 하는 정보  
```text 
- 데이터를 조회~출력하기까지 여러 객체들이 사용되고 있다.   
- DB로 조회된 데이터는 캐싱하는 것을 잊지 않아야 한다.   
- 데이터를 가공하기 위해 Message라는 객체를 사용해야 함을 알아야 한다.   
```
➜ `파사드 패턴을 적용`하여 단순화 가능.    

</br>

- Facade.cs   
```c# 
public class Facade
{
    #region [fields]
    private DB _db;
    private Cache _cache;
    #endregion

    #region [constructor]
    public Facade()
    { 
        _db = new DB();
        _cache = new Cache();
    }
    #endregion

    public void Run(string name)
    {
        if (!_cache.TryFetch(name, out Row row)) {
            if (!_db.TryFetch(name, out row)) {
                Console.WriteLine($"{name} is not exists");
                return;
            }

            _cache.Put(row);
        }

        var msg = new Message(row);
        Console.WriteLine(msg.GetName());
        Console.WriteLine(msg.GetBirthDay());
        Console.WriteLine(msg.GetEmail());
    }
}
```  
  
- UnitTest.cs   
```c# 
public class Tests
{
    private DB _db;
    private Cache _cache;

    [SetUp]
    public void Setup()
    {
        _db = new DB();
        _cache = new Cache();
    }

    [Test]
    public void Test1()
    {
        var name = "dorosh";

        if (!_cache.TryFetch(name, out Row row)) {
            if (!_db.TryFetch(name, out row)) {
                Console.WriteLine($"{name} is not exists");
                return;
            }

            _cache.Put(row);
        }

        var msg = new Message(row);
        Console.WriteLine(msg.GetName());
        Console.WriteLine(msg.GetBirthDay());
        Console.WriteLine(msg.GetEmail());
    }

    [Test]
    public void Test2()
    {
        var name = "dorosh";
        new Facade().Run(name);
    }
}
```  

</br>

📝 정리    
```text 
- 개발자는 위 기능을 사용하기 위해 필요한 여러 클래스들을 파악하지 않고, 파사드 객체만 알면 된다. 

- 다른 개발자에게 라이브러리나 패키지 형태로 코드를 제공할 때 파사드 패턴을 적용하면, 파사드 클래스만 오픈하고 그 외 나머지 클래스는 비공개 처리해도 된다. 
➜ 해당 라이브러리를 받아 쓰는 개발자 입장에서 부담이 줄게 됨.      
```



