# 폼 데이터 검증

### Validator
- 뷰에서 넘어온 데이터(커맨드 객체)를 `서버`에서 검증.(javascript를 통한 검증은 클라이언트에서 하는거)
- `Validator 인터페이스`를 구현한 클래스를 통해 컨트롤러에 넘어온 커맨드 객체에 대한 유효성 검사를 수행함.
- ex)
    ```java
    public class UserValidator implements Validator {

	@Override
	public boolean supports(Class<?> clazz) {	
		return false;
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
- 