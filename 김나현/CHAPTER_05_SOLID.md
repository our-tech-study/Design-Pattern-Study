[리스코프 치환 원칙(LSP)]


#정의

 다형성에 관한 원칙으로 상위 클래스 타입의 객체를 하위 클래스 타입의 객체로 치환해도 상위타입을 사용하는 코드는 원래의 의도대로 동작해야함
 = 서브클래스와 부모클래스는 언제나 호환될 수 있어야하며 서브클래스는 부모클래스의 행동규약을 준수해야함

 위반되는 경우 부모-서브 클래스 간의 is-a 관계가 무너지고 다형성에 기반한 개방 폐쇄 원칙 또한 지켜지지 않음


# 위반사례

 - 명시된 명세에서 벗어난 값 리턴 (2)
 - 명시된 명세에서 벗어난 예외 발생
 - 명시된 명세에서 벗어난 기능 수행 (1)
 
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
    
