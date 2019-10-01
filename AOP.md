```java
public class MainAspect {
	public static void main(String[] args) {
		System.out.println("메인 실행");
		
		AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AppCtx.class);
		
		Calculator cal = ctx.getBean("recCalculator",Calculator.class); //클래스 타입 RecCalculator.class 로 하면 에러남!! 의존 주입에 관해서는 문제 없음. 프록시 객체 관련 문제 생김.
		long result = cal.factorial(5);
		System.out.println("cal.factorial(5)=" + result);
		System.out.println(cal.getClass().getName()); //프록시 객체 찍힘.
		ctx.close();		
	}
}

```

```java
@Configuration
@ComponentScan(basePackages = {"com.book.chapter7.AOPtest"})
@EnableAspectJAutoProxy //@Aspect 붙은 빈 객체를 찾아서 빈 객체의 @Pointcut 설정과 @Around 설정을 사용함.
public class AppCtx {
	@Bean
	public Calculator recCalculator() {
		return new RecCalculator();
	}
```

  
<br/><br/><br/><br/><br/><br/><br/>
----
/**
		 * 프록시 객체가 찍히는 이유 !
		 * 스프링은 Aspect의 적용대상이 되는 객체에 대한 프록시를 만들어 제공함.
		 * 비즈니스 로직에 접근할 때 대상 객체로 바로 접근하는게 아닌 프록시를 통해 간접적으로 접근.
		 * 즉, 실제 비즈니스 로직이 담긴 메서드는 프록시 객체가 호출해 주는 셈.
		 * 프록시는 공통 기능을 실행한 뒤 대상 객체의 실제 메서드를 호출하거나 또는 대상 객체의 실제 메소드를 호출한 뒤 공통기능을 실행함.
		 * */