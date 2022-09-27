
[다형성과 상속]

# 상속
    - 클래스를 확장하는 기법으로 클래스에 다른 클래스의 데이터 또는 기능을 물려주는 것
    - 부모(상위)클래스와 자식(하위)클래스로 계층구조 생성

# 다형성
    - 부모클래스의 필드/메서드를 상속받고 있는 자식클래스는 부모클래스 타입으로 사용가능 : 형태의 다양화


using System;


public class Parent_A
{
    // 오버라이드를 사용하기 위해서 virtual 한정자 명시
    virtual void getName()
    {
        Console.WriteLine("A");
    }

    virtual void sealed()
    {
        Console.WriteLine("A");
    }
}

public class Child_B : Parent_A
{
    // 오버라이드
    // 상위클래스의 메서드를 재정의
    // 좁은 범위로의 재정의가 불가능 : default > private
    public override void getName()
    {
        Console.WriteLine("A_b");
    }

    // sealed 한정자를 사용하여 재정의할 경우 더이상의 재정의 방지
    public sealed override void sealed()
    {
        Console.WriteLine("A_Sealed");
    }
}

public class Child_C : Child_A
{
    
}

public class Child_D : Child_B
{
    public override void getName()
    {
        Console.WriteLine("Ab_d");
    }

    /*
    public sealed override void sealed()
    {
        // 클래스 Child_B에서 더이상의 재정의를 막음
    }
    */
}


interface IinterE
{
	void print()
	{
		Console.WriteLine("IE");
	}
}

// 인터페이스의 경우 다중상속 가능
public class Child_F : Child_B, IinterE
{
    // 상속받은 인터페이스의 메서드를 재정의 할 때는 public 사용
	// override 사용하지 않음
}


public class Program
{
    static void Main(string[] args)
    {
        Parent_A a = new Parent_A();
        a.getName(); // A
        Child_B b = new Child_B();
        b.getName(); // Ab
        Child_C c = new Child_C();
        c.getName(); // A


        // Child_D타입 객체를 생성해서 Parent_A타입 변수에 할당
        // 부모클래스의 참조변수는 자식 객체를 참조할 수 있음 - 다형성
        Parent_A a2 = new Child_D();

        // 업캐스팅
        // 하위클래스를 상위클래스로 사용
        a2.getName(); // Ab_d
        a2.sealed(); // A_Sealed


        Child_B b = a2; // Parent_A/Child_B를 식별하지 못함

        // 다운캐스팅
        Child_B b = (Child_B)a2; 
        b.getName(); // A_b
		
		
		// 인터페이스 상속
		Child_B b2 = new Child_F;
		
        b2.print();
    }
}
