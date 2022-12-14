[리스코프 치환 원칙(LSP)]



#정의

 다형성에 관한 원칙으로 상위 클래스 타입의 객체를 하위 클래스 타입의 객체로 치환해도 상위타입을 사용하는 코드는 원래의 의도대로 동작해야함
 = 서브클래스와 부모클래스는 언제나 호환될 수 있어야하며 서브클래스는 부모클래스의 행동규약을 준수해야함

 위반되는 경우 부모-서브 클래스 간의 is-a 관계가 무너지고 다형성에 기반한 개방 폐쇄 원칙 또한 지켜지지 않음
 
 

#요구사항

 - 하위 클래스에서 선행조건은 강화될 수 없음(= 추가될 수 없음)
		: 메소드가 실행되기 위한 조건(전달 파라미터 등)이 하위클래스에서 추가되는 것을 피해야 함
		   상위 클래스와 동일한 수준의 선행조건으로 생각하고 사용하는 하위클래스 로직에서 예상치 못한 오류 발생할 수 있음
		   
 - 하위 클래스에서 후행조건은 약화될 수 없음
		: 메소드가 호출된 후에 데이터의 유효성을 확인하는 조건으로 리턴 값이나 참조 파라미터로 전달된 값의 정의 확인
 
 - 하위클래스에서 상위클래스의 불변 조건은 반드시 유지되어야 한다.
		: 부모클래스에서 정의한 값의 조건은 하위클래스에서도 유지되어야 함
  


# 위반사례

 - 명시된 명세에서 벗어난 값 리턴 (2)
 - 명시된 명세에서 벗어난 예외 발생
 - 명시된 명세에서 벗어난 기능 수행 (1)



 ![Liskov1](https://user-images.githubusercontent.com/67131292/194946315-a547b8c0-f4b7-47cf-8d6b-738be22498cc.png)

 
 1-1.
 
    class Rectangle
    {
        public int Width { get; set; } // 가로
        public int Height { get; set; } // 세로

        public virtual void SetWidth(int width)
        {
            this.Width = width;
        }

        public virtual void SetHeight(int height)
        {
            this.Height = height;
        }

        public int GetWidth()
        {
            return Width;
        }

        public int GetHeight()
        {
            return Height;
        }
        
    }

    class Squre : Rectangle
    {
        public override void SetWidth(int width)
        {
            this.Width = width;
            this.Height = width;
        }

        public override void SetHeight(int height)
        {
            this.Height = height;
            this.Width = height;
        }
    }
    
    
    class Program
    {
        static void Main(string[] args)
        {
            Draw draw = new Draw();
            Rectangle rec = new Rectangle();

            rec.SetHeight(10);
            rec.SetWidth(5);

            Console.WriteLine(rec.GetHeight()); // 10
            Console.WriteLine(rec.GetWidth()); // 5

            draw.IncreaseHeight(rec);

            Console.WriteLine(rec.GetHeight()); // 20
            Console.WriteLine(rec.GetWidth()); // 5


            Squre sqr = new Squre();
            sqr.SetHeight(10);
            sqr.SetWidth(5);

            draw.IncreaseHeight(sqr);

            Console.WriteLine(rec.GetHeight()); // 15
            Console.WriteLine(rec.GetWidth()); // 15

        }
    }
 
 
 IncreaseHeight() 메소드 실행 후에는 width보다 height의 값이 더 크다고 가정할 수 있지만 Squre 타입 객체를 매개변수로 전달하면 가정과는 다른 결과 출력

개념적으로는 상속관계에 있는 것 처럼 보이지만 실제 구현 시에는 상속관계가 아닐 수도 있음



1-2.

    class Ellipse
    {
        public double MajorAxis { get; set; } // 긴 반지름
        public double MinorAxis { get; set; } // 짧은 반지름

        public virtual void SetMajorAxis(double majorAxis)
        {
            MajorAxis = majorAxis;
        }

        public virtual void SetMinorAxis(double minorAxis)
        {
            MinorAxis = minorAxis;
        }

        public virtual double Area()
        {
            //return MajorAxis * MinorAxis * Math.PI;
            return MajorAxis * MinorAxis;
        }
        
    }

    class Circle : Ellipse
    {
        public override void SetMajorAxis(double majorAxis)
        {
            base.SetMajorAxis(majorAxis);
            this.MinorAxis = majorAxis; // 원은 반지름이 동일
        }

        // 중복되는 데이터 및 로직 
        // 리스코프 치환 원칙이 위반되면 기능 확장을 위해 같은 소스를 여러번 수정하는 빈도가 높아짐
        //public override void SetMinorAxis(double majorAxis)
        //{
        //}
    }
    
    
    class Program
    {
        static void Main(string[] args)
        {
            Circle circle = new Circle();
            circle.SetMajorAxis(5);
            circle.SetMinorAxis(4);

            var area = circle.Area();
            Console.WriteLine(area); //20

        }
    }
    
    
Circle Class에서 SetMajorAxis 메소드는 수정되었지만, 수정되지 않은 SetMinorAxis 메소드는 버그를 발생
 
 → 원의 경우 모든 반지름이 동일한데 SetMinorAxis 메소드에서는 MajorAxis 값을 재할당하는 로직이 구현되어 있지 않으므로 MajorAxis, MinorAxis 값이 서로 다르고  예상과 결과가 다를 수 있음


    class Circle
    {
        public double Radius { get; set; } // 반지름

        public void SetRadius(double radius)
        {
            this.Radius = radius;
        }

        public double Area()
        {
            //return this.Radius * this.Radius * Math.PI;
            return this.Radius * this.Radius;
        }
        
    }
    
Circle 클래스를 재구현하거나, 
    
    abstract class Shape
    {
        public abstract double Area();
    }

    public class Ellipse : Shape
    {
        public override double Area()
        {
            return MajorAxis * MinorAxis * Math.PI;
        }
    }

    public class Circle : Shape
    {
        public override double Area()
        {
            return this.Radius * this.Radius * Math.PI;
        }
    }
    
추상클래스를 활용하여 각 클래스와의 관계를 유연하게 설계



2.

    class CopyUtil
    {
        public void Copy(String word, StreamReader input, StreamWriter output)
        {
            var buffer = word.ToCharArray();

            while (input.Read(buffer, 0, buffer.Length) != 0)
            {
                output.Write(buffer);
            }
        }
    }
    
  
더이상 읽어올 문자열이 없는 경우 0을 리턴하므로 Copy() 메소드는 리턴값이 0이 아닐 때까지 반복해서 데이터를 읽고 씀

StreamReader를 상속받는 하위 클래스에서 read()메소드를 재정의하고 더이상 읽어올 데이터가 없을 때 0이 아닌 다른 값을 리턴하도록 구현하면, Copy()메소드는 무한루프를 돌면서 실행이 끝나지 않게 됨 






#정리

서브클래스는 부모클래스에서 정의한 명세를 벗어나지 않는 범위에서 구현해야함
올바르지 못한 상속관계는 제거하고 부모 객체의 동작을 대체할 수 있는 관계만 상속하도록 코드 설계
