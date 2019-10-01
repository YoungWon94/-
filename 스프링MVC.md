# 스프링MVC

### 핵심 구성 요소
- `DispatcherServlet` : 모든 연결을 담당. 웹브라우저(클라이언트)로 부터 요청을 받고 응답함.(요청에 대한 처리는 컨트롤러에서 함.)
- `HandlerMapping` : 클라이언트의 요청 경로를 이용해 요청을 처리할 컨트롤러 빈 객체를 DispatcherServlet에 전달.
- `HanlderAdapter` : DispatcherServlet는 HandlerMapping이 찾아준 컨트롤러 객체의 처리를 HandlerAdapter에게 위임. HandlerAdapter는 컨트롤러 객체에서 알맞는 메서드를 호출해 요청을 처리하고 결과를 ModelAndView 객체로 변환하여 DispatcherServlet에 리턴함.
- `Controller` : 클라이언트의 요청을 실제로 처리.(DispatcherServlet은 요청을 전달받는 창구 역할)
- `ViewResolver` : ModelAndView 객체에 담겨져 있는 뷰 정보를 이용해 해당하는 View객체를 찾거나 생성하여 DispatcherServlet에 리턴.(응답을 생성하기 위해 jsp를 사용하는 뷰리졸버는 매번 새로운 View객체를 생성하여 DispatcherServlet에 리턴.)


### 요청 처리 순서.
1. 웹브라우저(클라이언트)에서 `요청 전송`.
1. `DispatcherServlet`이 요청을 받고 HandlerMapping 빈 객체에게 요청 URL과 매칭되는 컨트롤러 검색을 요청.  
`HandlerMapping`은 전달 받은 요청 경로를 이용해 이를 처리할 Controller 빈 객체를 DispatcherServlet에게 전달.
1. DispatcherServlet은 HandlerMapping에게 전달받은 Controller 객체의 처리를 `HandlerAdapter`에게 위임하고,
HandlerAdapter는 컨트롤러의 알맞는 메서드를 호출.
1. 호출된 `Controller`의 메서드에서 요청을 처리한 뒤 다시 `HandlerAdapter`에게 결과를 리턴함.
처리하고 결과를 `ModelAndView` 객체로 변환해 DispatcherServlet에게 리턴.
1. ModelAndView 객체를 전달 받은 `DispatcherServlet`은 `ViewResolver`에게 컨트롤러의 실행 결과를 보여줄 뷰를 찾아줄 것을 요청.
1. `ViewResolver`는 ModelAndView에 담겨있는 뷰 정보를 이용해 `View`객체를 생성해 `DispatcherServlet`에 리턴함.
1. DispatcherServlet은 리턴받은 `View` 객체에 응답 결과 생성을 요청. (jsp사용할 경우)View객체는 jsp를 실행함으로써 웹브라우저에 전송할 `응답` 결과를 생성하고 응답함. http 통신 종료됨.
