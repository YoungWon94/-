# 폼 데이터 검증

### Validator 인터페이스 사용
- 뷰에서 넘어온 데이터(커맨드 객체)를 `서버`에서 검증.(javascript를 통한 검증은 클라이언트에서 하는거)
- `Validator 인터페이스`를 구현한 클래스를 통해 컨트롤러에 넘어온 커맨드 객체에 대한 유효성 검사를 수행함.
- ex)
    ```java
    public class UserValidator implements Validator {

	@Override
	public boolean supports(Class<?> clazz) {	
		return User.class.isAssignableFrom(clazz); //검증할  객체의 클래스 타입 정보
	}

    /* 유효성 검사하는 메서드. target에 커맨드 객체 넘어옴, errors에는 컨트롤러에서 바인딩 변수 넘어옴..*/
	@Override
	public void validate(Object target, Errors errors) {
		System.out.println("user validator");
		User user = (User)target;
		
		String userId = user.getId();
		String userPw = user.getPw();
		
		if(userId.trim().isEmpty()) {
			System.out.println("아이디 공백임");
			errors.rejectValue("id", "id is empty");
		}else if(userPw.trim().isEmpty()) {
			System.out.println("비번 공백임");
			errors.rejectValue("pw", "pw is empty");
		}	

	    }
    }

    ```
    ```java
    @RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(User user, BindingResult result) {
		
		UserValidator uv = new UserValidator();
		uv.validate(user, result);
		if(result.hasErrors()) return "redirect:/"; //에러 있을 경우
		else return "loginSuccess";
	}
    ```
    - `BindingResult` result는 유효성 검사 후 결과(에러)를 담음.
        - 에러 없을 때 sysout(result)
        org.springframework.validation.BeanPropertyBindingResult: 0 errors
user validator
        - 에러 있을 때 sysout(result)
            org.springframework.validation.BeanPropertyBindingResult: 1 errors
            Field error in object 'user' on field 'pw': rejected value []; codes [pw is empty.user.pw,pw is empty.pw,pw is empty.java.lang.String,pw is empty]; arguments []; default message [null]
----
### ValidationUtils 클래스 사용
- Validator 인터페이스를 구현한 클래스의 validate() 메서드 대신 ValidationUtils 클래스가 제공하는 스태틱 메서드로 폼 데이터(커맨드 객체) 검증
- ex)
	```java
	@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(User user, BindingResult result) {	

		ValidationUtils.rejectIfEmptyOrWhitespace(result, "id", "EMPTY_id");//result : 검증 결과 담김. "id" : 커맨드객체(dto)의 필드, "EMPTY_id" : 에러코드
		ValidationUtils.rejectIfEmptyOrWhitespace(result, "pw", "EMPTY_pw");
		if(result.hasErrors()) {			
			return "redirect:/";
		}
		else {
			return "loginSuccess";
		}
	}
	```

----
### @Valid, @InitBinder
- 인터페이스나 메서드를 직접 호출하지 않고 알아서 검증해줌.
- 의존 라이브러리 추가
	```xml
	<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
	<dependency>
    	<groupId>org.hibernate.validator</groupId>
    	<artifactId>hibernate-validator</artifactId>
    	<version>6.0.16.Final</version>
	</dependency>

	```
- Validator 인터페이스를 구현한 클래스 선언(소스 위와 동일.)	
- 핸들러 선언
	```java
	@InitBinder
	protected void initBinder(WebDataBinder binder) {		
		binder.setValidator(new UserValidator());
	}
	```
- 커맨드 객체에 `@Valid` 으로 데이터 검증
	```java
	@RequestMapping(value="/login", method=RequestMethod.POST)
	public String login(@Valid User user, BindingResult result) {		

		if(result.hasErrors()) {
			System.out.println(":::"+result);
			return "redirect:/";
		}
		else {
			System.out.println(":::"+result);
			return "loginSuccess";
		}
	}
	```