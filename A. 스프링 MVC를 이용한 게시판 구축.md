# 스프링 MVC를 이용한 게시판 구축

### URL과 @RequestMapping 연결하기
웹 상에서 클라이언트의 요청은 모두 URL 기반으로 이뤄진다. 웹 서버는 클라이언트가 요청한 URL정보를 이용해 서버의 특정 구성 요소에서 서비스를 처리한 후 
그 결과를 클라이언트에게 응답으로 보내준다. 스프링 MVC에서는 @Controller 어노테이션이 붙은 클래스 안에 @RequestMapping 어노테이션이 붙은 메서드에서
클라이언트 요청을 처리하게 된다.

클라이언트가 URL을 입력하고 웹 서버에 서비스를 요청하면 서블릿 컨테이너가 웹 컨텍스트를 찾고 해당 웹 컨텍스트는 스프링 ApplicationContext에게
URL 중에 웹 컨텍스트 뒤를 처리할 수 있는, @RequestMapping(value="")를 가진 메서드에게 처리를 위임한다.

웹 경로 | 의미 | 메타포(전화 통화)
--------|--------|------------
http:// | 프로토콜: 규약, 의정서 | 한국어로 대화하자
localhost | URL(IP): 네트워크 상의 컴퓨터 식별자 | 상대방 집 전화번호
:8080 | 포트: 컴퓨터에서 구동되고 있는 프로그램 식별자 | 상대방 집 구성원 중 한사람
/spring | 컨텍스트: 프로그램 메뉴 그룹 식별자 | 대화 주제
/ | 경로: 메뉴 그룹 중에 메뉴 하나를 식별 | 한마디 대화
<hr/>

### dependency 추가 방법
pom.xml파일을 열고 <dependencies>태그를 찾아 그 아래에 내용을 넣는다.

ex)
```
<!-- jdbc -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-jdbc</artifactId>
	<version>${org.springframework-version}</version>
</dependency> 
```
EL 형식으로 표시된 ${org.springframework-version} 변수명은
```
<properties>
  <java-version>1.6</java-version>
	<org.springframework-version>3.1.1.RELEASE</org.springframework-version>
	<org.aspectj-version>1.6.10</org.aspectj-version>
	<org.slf4j-version>1.6.6</org.slf4j-version>
</properties>
```
> 위의 <properties> 태그 안의 스프링 버전 변수명과 일치하지 않는 경우 일치시킨다.

### VO(Value Object 또는 DTO[Data Transfer Object])
VO는 일반적으로 데이터베이스의 테이블 구조를 그대로 반영한다. 

### 서비스
일반적으로 DAO는 데이터베이스 테이블당 하나를 만들게 된다. 하지만 사용자에게 제공되는 서비스는 여러 테이블의 정보를 조합해서 제공하는 경우가 많다.
따라서 하나의 서비스에서 다수의 DAO를 사용하기도 하고 때로는 다수의 서비스가 하나의 DAO를 사용하기도 한다. 물론 대부분의 경우 하나의 서비스가
하나의 DAO와 관계를 맺게 된다. 게시판을 작성하다 보면 서비스가 단순히 DAO에게 위임하는 형태로 구성되는 경우가 많다. 이런 경우 서비스의 필요성에
대해 의구심을 갖는 개발자들이 있는데, **확장성과 유연성을 고려하면 서비스를 작성하는 것이 실보다 득이 더 많다.** 또한, 서비스는 DAO와의 연동뿐만
아니라 서버 기술(웹, 클라이언트/서버)이나 각 벤더별 데이터베이스에 종속되지 않는 로직을 구현하는 곳이기도 하기에 반드시 작성하는 습관을 들이자.

### 모델
모델(Model) 모델은 컨트롤러에서 뷰로 전달해주는 정보다. 스프링 MVC에서 모델을 생성하는 것은 DispatcherServlet의 역할이다.<br/>
DispatcherServlet이 생성한 모델에 대한 참조 변수는 @RequestMapping 어노테이션이 붙은 메서드에서 인자를 선언하기만 하면 자동으로 받을 수 있다.

> 액션 메서드가 리턴하는 뷰의 힌트를 이용해 스프링 MVC의 DispatcherServlet은 사용자에게 보여줄 뷰를 선정한다.

※ JSP 파일에서는 jstl과 el을 이용해 결과를 표시한다.<br/>
※ 컨트롤러에서 모델에 담아서 보내준 정보는 EL표기법을 이용해 쉽게 접근해서 사용할 수 있다.

##### @PathVariable 어노테이션
경로 변수를 메서드의 인자로 사용하기 위한 어노테이션

> 경로 변수의 값이 자동으로 바인딩 된다.
> 기존 방식에서 request.getParameter("seq")를 다시 int 형으로 변환하는 단순하고 지루한 작업을 스프링 MVC가 완벽하게 대신 처리해준다.

#### GET, POST
@RequestMapping에서 호출 방식이 GET 방식이냐 POST 방식이냐를 구분해서 같은 URL로 요청이 들어와도 별개의 메서드가 처리할 수 있게 지원한다.

a 태그의 링크를 클릭해서 들어오거나 브라우저 주소창에 직접 주소를 입력해 들어온 경우 method 속성 값이 RequestMethod.GET으로 지정된 메서드가 호출되고, form 태그의 method 속성을 POST로 지정해 서버로 전송된 경우에는 method 속성의 값이 RequestMethod.POST로 지정된 write()메서드가 호출된다.

POST 요청을 처리하는 메서드는 인자로 보통 DTO객체를 받는데 스프링 MVC는 form 태그에서 전송된 input 태그의 name 속성과 DTO 인스턴스의 속성 이름을 비교해 자동으로 그 값을 바인딩해 준다. 스프링 MVC를 사용하지 않았다면 사용자가 입력 태그를 통해 입력한 내용을 HttpServletRequest의 인스턴스(request)에서 getParameter() 메서드를 이용해 가져온 후 DTO 인스턴스의 속성에 넣어주기 위해 형변환까지 해야 했을 것이다. 이처럼 스프링 MVC는 지루하고 반복적인 작업을 대신 알아서 해준다.

###### BindingResult 사용하기
GET 요청을 처리하는 메서드에서는 DTO의 인스턴스를 생성해 MVC 요소 중 모델에 추가하고, POST 요청을 처리하는 메서드에서는 BindingResult의 인스턴스를 인자로 받는다. BindingResult는 자신의 바로 앞에 있는 인자, 즉 DTO에 사용자로부터 입력된 값을 바인딩할 때 오류가 발생하는 경우 오류 내용을 자동으로 저장해서 갖고 있게 된다. BindingResult 객체의 메서드 중 hasErrors() 메서드를 호출하면 바인딩할 때 오류가 있는 경우 true를 반환한다. 이 때 Controller에 org.springframework.validation.BindingResult를 임포트해야 한다.

###### 스프링 form 태그 사용하기
스프링 form 태그를 사용할 경우 form 태그에는 action 정보가 없는데 브라우저 주소창을 참고해 스프링이 자동으로 action 정보를 설정해준다.

### PRG 패턴
요청을 리디렉션(Redirect)해서 GET 요청으로 보내는 것을 PRG(POST-Redirect-GET) 패턴이라고 한다.
> PRG 패턴을 사용하는 이유
* 새 글을 성공적으로 저장한 후의 뷰가 계속해서 그대로 남아있게 되면 사용자가 화면 새로 고침 버튼을 누르면 다시 POST 요청이 서버로 전송되고 같은 글이 다시 데이터베이스에 저장된다. 화면을 새로 고침할 때마다 같은 글이 데이터베이스에 저장되는 것이다.

###### 한글 깨짐 현상
> web.xml파일에 filter 작성하기

##### 세션(session) 이용하기
* MVC 컨트롤러에 @SessionAttributes("Session에 저장할 객체명") 어노테이션을 지정한다.
* 세션에서 저장한 객체를 활용하는 edit() 메서드의 인자에 @ModelAttribute 어노테이션을 지정한다.

###### @SessionAttributes("DTO객체")
> DTO객체라는 이름으로 객체가 MVC의 모델에 추가될 때 세션에도 DTO객체를 저장하라고 지정한다.
###### model.addAttribute("key", value);
> MVC의 모델에 key라는 이름으로 객체를 추가하면 @SessionAttributes에 의해 자동으로 세션에도 추가된다.
###### @ModelAttribute DTO 인스턴스
> POST 요청을 처리하는 메서드의 인자인데, HttpServletRequest를 이용해 자동으로 바인딩될 것이다. @SessionAttributes에서 인스턴스가 지정된 경우 세션에 의한 바인딩이 먼저 실행되고, 그 후에 HttpServletRequest에 있는 정보로 갱신된다. 이 때 HttpServletRequest에 존재하지 않는 속성은 세션에 값을 유지하게 된다.
###### SessionStatus sessionStatus
> POST 요청을 처리하는 edit() 메서드의 인자인데, 세션에 대한 작업을 처리할 수 있는 객체다.
###### sessionStatus.setComplete();
> @SessionAttributes에 의해 세션에 저장된 객체를 제거하는 코드다.

### 리팩터링 해보기!!!
