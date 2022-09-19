
[캡슐화]

객체지향 :
	한 곳의 구현변경이 다른 곳에 영향을 미치지 않게 원활한 수정을 지향
	객체 내부의 기능 구현 방식을 감추고 기능의 요소에 접근하는 것이 아닌 기능 자체를 사용하도록 해서 구현이 변경되더라도 영향을 받지 않도록 만들어줌 → 내부 구현 변경의 유연함↑
	
	= 캡슐화(Encapsulation)
	

> 예시 - 만료여부 체크
	
	#절차지향
	
	using System;

	public class Member
	{
	    private DateTime expiryDate;
	    private Boolean male;

	    public DateTime getExpiryDate()
	    {
		return expiryDate;
	    }

	    public Boolean isMale()
	    {
		return male;
	    }
	}

	public class 절차지향1
	{
	    public void example1(Member member)
	    {
		if (member.getExpiryDate() != null && member.getExpiryDate() < DateTime.Now) {
		    //
		}
	    }

	    public void example2(Member member)
	    {
		if ((member.isMale() && member.getExpiryDate() != null && member.getExpiryDate() < DateTime.Now)
		     || (!member.isMale() && member.getExpiryDate() != null && member.getExpiryDate() < DateTime.Now.AddMonths(-1))) 
		{
		    //
		}
	    }
	}

	여러 곳에서 사용되고 있는 만료여부 확인코드를 모두 찾아서 변경
	사용되는 곳이 많을 수록 변경 시 수정이 누락될 확률이 높아지고 수정시기와 오류발생시기에 차이가 클 수록 원인 발견이 어려움
	
	--
	데이터를 직접적으로 사용하는 코드는 데이터 변화에 직접적인 영향을 받으므로 요구사항의 변화에 따라 데이터 구조 또는 용도가 변경될 경우 연쇄적 수정이 필요


	#객체지향
	
	public class Member
	{
	    private DateTime expiryDate;
	    private Boolean male;

	    public Boolean isExpired()
	    {
		return (expiryDate != null && (expiryDate.getDate() < DateTime.Now));
	    }

	    public Boolean isExpired()
	    {
		if (male) {
		    return expiryDate != null && (expiryDate.getDate() < DateTime.Now);
		}
		else {
		    return expiryDate != null && (expiryDate.getDate() < DateTime.Now.AddMonths(-1));
		}
	    }
	}

	public class 객체지향1
	{
	    public void example(Member member)
	    {
		if (member.isExpired()) {
		    //
		}
	    }

	}
	
	기능이 캡슐화 대상이 되고 있음
	다른 클래스에서는 만료여부 체크 메소드 구현방식을 알지 못하고 만료여부에 따른 동작만 구현
	만료여부 체크 규칙이 변경되더라도 해당 메소드가 사용되는 코드는 수정 불필요
	
	--
	캡슐화된 기능을 사용하고 있으므로 수정 영향 없음
	내부 기능 구현 변경이 유연
	
	> Tell, Don't Ask
		데이터를 읽는 것이 아닌 기능(메소드)를 사용
		메소드를 구성하고 있는 변수에 접근제한자를 사용해서 데이터를 임의로 변경할 수 없도록 하는 방법 사용
		
	> 데미테르의 법칙
		- 메소드에서 생성한 객체의 메소드만 호출
		- 파라미터로 받은 객체의 메소드만 호출
		- 필드로 참조하는 객체의 메소드만 호출
