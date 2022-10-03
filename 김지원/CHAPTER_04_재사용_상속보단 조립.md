# Chapter 4-2 조립을 이용한 재사용

### 파일 보관을 기준으로

![image](https://user-images.githubusercontent.com/59535499/193647440-9a663248-cf8a-434f-9d38-aad1a82089fc.png)

"보관"에 초점  
중복되는 소스를 합쳐 상속을 시키는 개념으로 맞춰서 개발  
보관은 역할의 행동 중 하나인데 객체가 기준이 아닌 행동 중 하나에 초점을 맞추다보니 상속개념으로 흘러간다고 생각  

### 각 객체 역할을 기준으로

![image](https://user-images.githubusercontent.com/59535499/193647705-092978c3-d7ce-45e5-8339-36a5f3d5c2d6.png)

객체의 "역할"에 초점  
각 객체가 필요한 역할만 구현하고 다른 객체가 해당 기능이 필요하면 끌어다 써라(책에서 강조하는 조립)  
조립은 구현이 더 어렵지만 추부 변경에는 더 용이 

![image](https://user-images.githubusercontent.com/59535499/193648678-6a0e3201-5bfd-44df-b0cd-0519e150ad66.png)

상속이 무조건 나쁘다는 아님!  
상속보다 조립을 우선하는 이유(이전 글에 설명한 상속의 단점)  
조립보다 상속으로 개발될 때 객체의 역할을 한 번 더 생각하고 맞는지 판단하자.

## 2-2 상속은 언제 사용하나?

"기능의 재사용"이 아닌 "기능의 확장"

![image](https://user-images.githubusercontent.com/59535499/193649800-79824d3b-61df-464e-a395-3683b88a4ff4.png)

ex) Input
select , radio , checkbox , text , number 

상속한다 하더라고 고려해야할 사항  
- 클래스의 개수가 불필요하게 증가  
- 상위 클래스의 변경이 어려워 질 때  
- 하위 클래스가 상속받을 때 단점이 발생  
  -> 일부 구현을 조립해서 사용할 수 있도록 변경  
  
  
## 2-1 위임

내가 할 일을 다른 객체에게 넘김  

ex) 숫자를 입력하면 더하기만 해주는 계산기

"1,2,3"

```
class 계산기{
  계산(string 입력값){
    string[] 값목록 = 입력값.split(',')
    
    result = 0
    
    foreach 값 in 값목록{
      result += parse(값)
    }
  }
}
```
<br><br><br><br><br>
위 코드는 하나의 메서드에서 3가지 일을 하고 있다  
계산기에서 할 행동이 아닌 것 처럼 보이는 부분도 보인다.  
<br><br><br><br><br>

```
class 목록생성기{
  목록변환(string 입력값, string 구분자){
    return 입력값.split(구분자)
  }
}

class 파서{
  정수변환(string 값){
    return parse(값)
  }
}

class 계산기{
  계산(string 입력값){
    string[] 값목록 = 목록생성기.목록변환(입력값, ',')
    
    result = 0
    
    foreach 값 in 값목록{
      result += 파서.정수변환(값)
    }
  }
}
```
<br><br><br><br><br>
구분자가 변경됐을때?  
"1:2:3"
<br><br><br><br><br>
```



class 계산기{
  실행(string 입력값, string 구분자){
    string[] 값목록 = 계산값포맷화(입력값, 구분자)
    return 계산(값목록)
  }

  계산(string[] 값목록){       
    result = 0
    
    foreach 값 in 값목록{
      result += 파서.정수변환(값)
    }
    return result
  }
  
  계산값포맷화(입력값, 구분자){
    return 목록생성기.목록변환(입력값, 구분자)
  }
}
```

이런식으로 역할과 책임을 나누고 조립하자  
객체 지향과 디자인패턴에서 객체 지향에 다시 생각해보는 시간
