# 템플린 메서트 패턴

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
