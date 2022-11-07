# 템플릿 메서드 패턴

### 메서드 vs 메소드?

![image](https://user-images.githubusercontent.com/59535499/198889402-afc82e07-408c-4243-86e8-9224133e16f9.png)
 
## 예시

https://steady-coding.tistory.com/384  
 
```
class 신라면{
  라면끓이기{
    물끓이기
    면스프넣기
    계란넣기
    기다리기
  }
}

class 너구리{
  라면끓이기{
    물끓이기
    면스프넣기
    콩나물넣기
    기다리기
  }
}
```

### 패턴 적용 후

```
abstract 라면{
  라면끓이기{
    물끓이기
    면스프넣기
    토핑넣기
    기다리기
  }
  
  물끓이기
  추상 면스프넣기
  추상 토핑넣기
  기다리기
}

class 신라면 extends 라면{
  @오버라이드
  면스프넣기{
    면넣기
    스프넣기
  }
  
  @오버라이드
  토핑넣기{
    계란넣기
  }
}

class 너구리 extends 라면{
  @오버라이드
  면스프넣기{
    다시다넣기
    면넣기
    스프넣기
  }
  
  @오버라이드
  토핑넣기{
    콩나물넣기
  }
}
```

## 업무에서 쓰이는 예시

3.0  
OnExcuting  
OnExcute  
OnExcuted  
  
ESA팀  
쇼핑몰  
은행  
  
  
  
# 프록시 패턴

프록시 패턴은 어떤 다른 객체로 접근하는 것을 **통제**하기 위해서 그 객체의 대리자(surrogate)나 자리표시자(placeholder)의 역할을 하는 객체를 제공하는 패턴

```
class 대표
class 부장
class 과장
class 대리
class 사원
```

```
대표, 부장
public 결재(){
  결재 허용
}
```

휴가 결재를 올릴 때 허가할 수 있는 사람 : 대표 , 부장

```
public void 프록시(Person person, Command cmd){
  if(person is 대표 부장){
    cmd()  // == 결재()
  }
  else{
    throw new Exception
  }
}
```

https://readystory.tistory.com/132


# 프록시 패턴 vs 데코레이터 패턴

데코레이터 패턴은 **객체에 기능을 동적으로 추가**하는데 중점  
프록시 패턴은 객체에 대한 **액세스를 제어**하는데 중점

