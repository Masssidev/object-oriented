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

