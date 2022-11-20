# Chapter7. 주요 디자인 패턴 - 추상 팩토리 패턴

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
  public string Name => "gt";
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
  public string Name => "samchully";
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
  private IDictionary<string, IBikeFactory> _bikeFactories;
  
  public BikeManager()
  {
    this._bikeFactories = new Dictionary<string, IBikeFactory>();
  }
  
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




