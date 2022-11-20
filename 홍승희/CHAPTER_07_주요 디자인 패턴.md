# Chapter7. 주요 디자인 패턴 - 추상 팩토리 패턴

</br> 

## 목차 
1. [추상 팩토리 패턴](#1.-추상-팩토리-패턴-abstract-factory-pattern)
2. 

</br>

## 1. 추상 팩토리 패턴(Abstract Factory Pattern)  
- 다양한 구성 요소 별로 `객체의 집합`을 생성해야 할 때 유용한 패턴.  
- 관련성 있는 여러 종류의 객체를 `일관된 방식으로 생성`하는 경우 유용한 패턴.  

<br/>

## 2. 샘플  
  
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

## 2. 샘플  

- ex. DB에서 데이터를 `조회`해서 `가공` 후 출력하는 기능
```text
1. Cache 조회
2. DB 조회 
3. Cache에 입력
4. 조회 결과 가공
5. 가공된 결과 출력
```   

</br>

```bash
├── FacadePattern.csproj 
│   ├── Row.cs 
│   ├── Cache.cs 
│   ├── Db.cs 
│   ├── Message.cs
│   └── Facade.cs 
│    
└── TestProject.csproj
``` 

</br>

- Row.cs   
```c# 

```


  







