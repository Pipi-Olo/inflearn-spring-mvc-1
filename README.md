# 웹 애플리케이션 이해
## 웹 서버, 웹 애플리케이션 서버
### 웹 서버 (Web Server)
* HTTP 기반으로 동작한다.
* HTML, CSS, JS, 이미지 등 정적 리소스를 제공한다.
* NGINX, APACHE

### 웹 애플리케이션 서버 (Web Application Server)
* HTTP 기반으로 동작한다.
* 애플리케이션 로직을 수행한다.
  * 동적 HTML, HTTP API
  * 서블릿, 스프링 MVC, JSP
* 정적 리소스도 제공할 수 있다.
* Tomcat

### 웹 시스템 구성 - WEB, WAS, DB
![](https://velog.velcdn.com/images/pipiolo/post/7f1b44b5-b6b6-4653-94b5-628986ca4eb5/image.png)

* 웹 서버
  * 정적 리소스를 담당한다.
  * 동적인 처리는 WAS에 위임한다.
* WAS  
  * 애플리케이션 로직을 처리한다.
* 장점
  * 독립적인 운용을 통해 효율적인 리소스 관리가 가능하다.
    * 정적 리소스가 많이 사용되면 웹 서버 증설
    * 애플리케이션 리소스가 많이 사용되면 WAS 증설
  * WAS, DB 장애시 웹 서버가 오류 화면을 제공한다.
    * 정적 리소스만 제공하는 웹 서버는 잘 안 죽는다.
    * 애플리케이션 로직이 동작하는 WAS 서버는 잘 죽는다.

## 서블릿
```java
@WebServlet(name = "HelloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

	@Override
    protected void service(HttpServletRequest request, HttpServletResponse response) {
    	// 애플리케이션 로직 실행
    }
}
```

![](https://velog.velcdn.com/images/pipiolo/post/c8125f9e-ee38-44fa-82b8-75aeb8d356b3/image.png)

* 서블릿을 지원하는 WAS 사용하면, 비지니스 로직 실행에만 집중할 수 있다.
  * 소켓 연결, HTTP 메시지 파싱 등은 WAS 가 담당한다.
* 개발자는 HTTP 를 매우 편리하게 사용할 수 있다.
  * `HttpServletRequest` 👉 HTTP 요청 정보
  * `HttpServletResponse` 👉 HTTP 응답 정보
  * Request∙Response 객체를 통해 HTTP 요청∙응답에 대한 정보를 읽고∙입력하면 된다.
    * WAS가 HTTP 요청 메시지를 통해 Request 객체를 생성한다.
    * WAS가 Response 객체를 통해 HTTP 응답 메시지를 생성한다.
  
### 서블릿 컨테이너
![](https://velog.velcdn.com/images/pipiolo/post/c0564e2b-e044-4a0b-9e4a-82ee6c55ef3c/image.png)

* 서블릿을 지원하는 WAS를 **서블릿 컨테이너**라 한다.
* 서블릿 컨테이너는 서블릿 객체의 생성∙초기화∙호출∙종료 등 생명 주기를 관리한다.
* 서블릿 객체는 싱글톤으로 관리된다.
  * 공유 변수 사용에 주의한다.
* 멀티 쓰레드 처리를 지원한다.
* Tomcat

> **참고**
> JSP도 서블릿으로 변환되어서 처리된다.
  
### 멀티 쓰레드 - 동시 요청  
![](https://velog.velcdn.com/images/pipiolo/post/c7e3fc11-3742-4911-adf1-19039e8fd72d/image.png)

* 쓰레드를 미리 생성해서 쓰레드 풀에 보관 및 관리한다.
  * 쓰레드 풀은 쓰레드 최대치를 미리 생성한다.
  * 이미 생성되어 있는 쓰레드를 풀에서 꺼내서 사용한다.
  * 사용을 종료하면 쓰레드 풀에 반납한다.
* 장점
  * 쓰레드를 생성 및 종료하는 비용이 절약된다.
    * 쓰레드를 매번 생성하면, 쓰레드 컨텍스트 스위치 비용이 발생한다.
  * 응답 시간이 빠르다.
  * 생성 가능한 쓰레드의 최대치가 있으므로 요청을 안전하게 처리할 수 있다.
    * 쓰레드 생성에 제한이 없으면, 메모리 임계점을 넘어서 서버가 죽을 수 있다.
* 멀티 쓰레드는 WAS가 지원한다. 
  * 개발자는 멀티 쓰레드를 신경쓰지 않아도 된다.
  * 개발자는 싱글톤 객체를 주의하면서 싱글 쓰레드 프로그래밍 하듯이 개발한다.
    
> **참고**
> WAS의 주요 튜닝 포인트는 최대 쓰레드 개수이다. 주어진 상황에 따라 천차만별이므로 성능 테스트를 통해 적정 숫자를 찾을 수 밖에 없다.
> 톰캣의 최대 쓰레드 기본 값은 200개이다.

## HTML을 어디서 만들지❔
### 서버 사이드 렌더링 (Server Side Rendering)
![](https://velog.velcdn.com/images/pipiolo/post/04e30fa6-6b81-426a-9293-8b700d2b3eb6/image.png)

* 서버에서 최종 HTML을 생성해서 클라이언트에 전달한다.
  * 주로 정적인 화면에서 사용된다.
* 관련기술 
  👉 JSP, Thymeleaf 
  👉 백엔드 개발자

### 클라이언트 사이드 레더링 (Client Side Rendering)
![](https://velog.velcdn.com/images/pipiolo/post/6b4b95cc-5034-46bc-9944-0f4e3d56e37d/image.png)

* HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성한다.
  * 주로 동적인 화면에서 사용된다.
  * 필요한 정보들은 서버에 요청해서 얻는다.
* 관련기술 
  👉 React, Vue, JS 
  👉 프론트 개발자

## 자바 백엔드 웹 기술 역사
* 서블릿 👉 HTML 생성이 어렵다.
* JSP 👉 HTML 생성은 편리하지만, 비지니스 로직과 섞여있다.
* MVC 패턴과 서블릿∙JSP 👉 모델, 뷰, 컨트롤러로 역할을 나누어 수행한다.
* Spring MVC 👉 공통 처리를 지원하는 프론트 컨트롤러 도입한다.
  * 애노테이션 기반의 Spring MVC 등장
  * 서버를 내장한 스프링 부트 등장
  * 빌드 결과(`Jar`)가 WAS 서버를 포함한다. 👉 빌드∙배포 단순화
    * 과거에는 `War` 파일을 생성해서 WAS에 배포함

---

# 서블릿
```java	
@ServletComponentScan
@SpringBootApplication
public class ServletApplication {
 
	public static void main(String[] args) {
		SpringApplication.run(ServletApplication.class, args);
	}
}
```

```java
@WebServlet(name="helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
    			throws ServletException, IOException {
    }
}
```

* `@ServletComponentScan` 👉 서블릿 컴포넌트를 자동 등록해준다.
* `@WebServlet` 👉 서블릿 애노테이션
  * `name` 👉 서블릿 이름을 지정한다.
  * `urlPatterns` 👉 매핑할 URL 패턴을 지정한다.

> **참고**
> 서블릿은 톰캣 같은 웹 애플리케이션 서버를 설치하고 그 위에 서블릿 코드를 클래스 파일로 빌드해서 사용한다.
> 스프링 부트는 톰캣 서버를 내장하고 있으므로 WAS 설치 없이, 편리하게 서블릿 코드를 실행할 수 있다.

## HttpServletRequest
```java
/**
  * URL : http://localhost:8080/request?username=hi
  */
@WebServlet(name="requestServlet", urlPatterns = "/request")
public class RequestServlet extends HttpServlet {
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
    			throws ServletException, IOException {
		
        System.out.println("request.getMethod() = " + request.getMethod()); // GET
        System.out.println("request.getRequestURL() = " + request.getRequestURL()); // http://localhost:8080/request
        System.out.println("request.getQueryString() = " + request.getQueryString()); // username=hi
        System.out.println("request.isSecure() = " + request.isSecure()); // HTTPS 사용 유무
        System.out.println("request.getServerPort() = " + request.getServerPort()); // Host Port
        
        ...
    }
}
```

* 서블릿은 HTTP 요청 메시지를 파싱해서 `HttpServletRequest` 객체에 담아서 제공한다.
* HTTP 요청 메시지
  * START LINE
    * HTTP 메소드
    * URL
    * 쿼리 스트링
    * 스키마, 프로토콜
  * 헤더
  * 바디
    * Form 파라미터 형식 조회
    * Message Body 데이터 조회
* `HttpServletRequest`∙`HttpServletResponse` 는 HTTP 요청∙응답 메시지를 편리하게 사용하도록 해주는 객체이다.


## HTTP 요청 데이터
* 클라이언트에서 서버에 데이터를 전송한다. 서버는 요청된 데이터를 읽는다.
* GET - 쿼리 파라미터
  * 메시지 바디 없이, URL 쿼리 파라미터를 통해 데이터를 전달한다.
  * /url**?username=hello&age=20**
  * 검색, 필터, 페이징에서 사용된다.
* POST - HTML Form
  * 메시지 바디에 쿼리 파라미터 형식으로 전달한다.
  * content-type: **application/x-www-form-urlencoded**
  * HTML Form에서 아용된다.
* HTTP Message Body
  * 메시지 바디에 데이터를 직접 전달한다.
  * 데이터 형식은 주로 JSON 사용한다.
    * content-type: **application/json**
  * POST, PUT, PATCH 등 다양한 HTTP 메소드가 사용된다.
  * HTTP API에서 사용된다.

### GET 쿼리 파라미터
```java
/**
  * URL : http://localhost:8080/request-query?username=kim&age=20
  */
@WebServlet(name="requestQueryServlet", urlPatterns = "/request-query")
public class RequestQueryServlet extends HttpServlet {
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
    			throws ServletException, IOException {

		String username = request.getParameter("username"); // kim
        String age = request.getParameter("age"); // 20
    }
}
```

* 메시지 바디 없이, URL 쿼리 파라미터를 통해 데이터를 전달한다.
* /url**?username=hello&age=20**
  * 쿼리 파라미터는 URL 다음에 온다.
  * `?` 으로 시작한다.
  * 추가 파라미터는 `&` 으로 구분한다. 

### POST HTML Form
```java
/**
  * URL : http://localhost:8080/request-html
  * content-type : application/x-www-form-urlencoded
  * message body : username=kim&age=20
  */
@WebServlet(name="requestHtmlServlet", urlPatterns = "/request-html")
public class RequestHtmlServlet extends HttpServlet {
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
    			throws ServletException, IOException {

		String username = request.getParameter("username"); // kim
        String age = request.getParameter("age"); // 20
    }
}
```
![](https://velog.velcdn.com/images/pipiolo/post/27856331-bbbb-4ac5-82b0-110e2117eca9/image.png)

* 메시지 바디에 쿼리 파라미터 형식으로 전달한다.
* content-type: **application/x-www-form-urlencoded**
* 클라이언트(웹 브라우저)는 두 방식에 차이가 있지만, **서버는 둘 다 같은 형식이다.**
  * `request.getParameter()` 는 GET - URL 쿼리 파라미터 형식과 POST - HTML Form 형식 둘다 지원한다.

> **참고**
> `content-type` 은 HTTP 메시지 바디의 데이터 형식을 지정한다.
> **GET - URL 쿼리 파라미터** 형식은 HTTP 메시지 바디를 사용하지 않기 때문에 `content-type` 이 없다.
> **POST - HTML Form** 형식은 HTTP 메시지 바디에 데이터를 전달하므로 `content-type: application/x-www-form-urlencoded` 으로 지정해야 한다.

### HTTP Message Body
```java
/**
  * URL : http://localhost:8080/request-json
  * content-type : application/json
  * message body : {"username" : "kim", "age" : 20}
  */
@WebServlet(name="requestJsonServlet", urlPatterns = "/request-json")
public class RequestJsonServlet extends HttpServlet {
    
 	private final ObjectMapper objectMapper = new ObjectMapper();
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response)
    			throws ServletException, IOException {

		String inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, UTF_8);
        
        HelloDTO helloDto = objectMapper.readValue(messageBody, HelloDTO.class); // JSON -> 객체 변환
    }
}
```

* HTTP API 에서 사용된다.
* 메시지 바디에 데이터를 직접 전달한다.
  * content-type: **applicaion/json**
  * message body: `{"username" : "kim", "age" : 20}`
* `ObjectMapper` 는 JSON 요청 데이터를 `HelloDTO.class` 객체로 변환한다.

> **참고**
> JSON 데이터를 파싱해서 자바 객체로 변환하려면 JSON 변환 라이브러리가 필요하다. Spring MVC 는 Jackson 라이브러리(`ObjectMapper`)를 사용한다.

## HttpServletResponse
```java
@WebServlet(name="responseServlet", urlPatterns = "/response")
public class ResponseServlet extends HttpServlet {
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response)
    			throws ServletException, IOException {
                
        // [status-line]
        response.setStatus(HttpServletResponse.SC_OK);
 
        // [response-headers]
        response.setHeader("Content-Type", "text/plain;charset-utf-8");
        response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
        response.setHeader("my-header", "hello"); // 커스텀 헤더
 
        // [Header 편의 메서드]
        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");
        
        Cookie cookie = new Cookie("myCookie", "good");
        cookie.setMaxAge(600);
        response.addCookie(cookie);
        
        response.sendRedirect("/basic/hello-form.html");
 
        // [message body]
        PrintWriter writer = response.getWriter();
        writer.println("ok");
    }
}
```

## HTTP 응답 데이터
* 서버에서 클라이언트로 데이터를 전송한다.
* 단순 테스트 응답
  * `writer.println("ok")`
* HTML 응답
* HTTP API
  * 메시지 바디에 JSON 형식 데이터를 전달한다.

### 단순 텍스트 응답
### HTML 응답
```java
@WebServlet(name = "responseHtmlServlet", urlPatterns = "/response-html")
public class ResponseHtmlServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response)
    			throws ServletException, IOException {
                
        // Content-Type : text/html; charset=utf-8
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
 
        // HTML Response
        PrintWriter writer = response.getWriter();
        writer.println("<html>");
        writer.println("<body>");
        writer.println("<div>HELLO?</div>");
        writer.println("</body>");
        writer.println("</html>");
    }
}
```
* HTML 응답은
  * content-type: **text/html**

### HTTP API 
```java
@WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
public class ResponseJsonServlet extends HttpServlet {
    
    private ObjectMapper objectMapper = new ObjectMapper();
 
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response)
    			throws ServletException, IOException {
                
        // Content-Type: application/json
        response.setContentType("application/json");
 
        HelloData helloData = new HelloData();
        helloData.setUsername("kim");
        helloData.setAge(20);
 
        // {"username":"kim", "age":20}
        String result = objectMapper.writeValueAsString(helloData);
        response.getWriter().write(result);
    }
}
```

* content-type: **application/json**
* `objectMapper.writeValueAsString()` 👉 객체를 JSON 문자로 변경한다.

> **참고**
> `application/json` 은 `utf-8` 을 사용하도록 되어 있다. `charset=utf-8` 같은 추가 파라미터를 지원하지 않는다. 따라서 `application/json;charset=utf-8` 은 불필요한 파라미터만 추가된 것이다.

---

# MVC 패턴과 서블릿∙JSP
## 서블릿
```java
@WebServlet(name = "responseServlet", urlPatterns = "/response/servlet")
public class ResponseServlet extends HttpServlet {

    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
    			throws ServletException, IOException {
                
        // Request
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
 
 		// Business Logic
        Member member = new Member(username, age);
       	memberRepository.save(member);
 
        // Response HTML
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        
        PrintWriter w = response.getWriter();
        w.write("<html>\n" +
                "<head>\n" +
                " <meta charset=\"UTF-8\">\n" +
                "</head>\n" +
                "<body>\n" +
                "성공\n" +
                "<ul>\n" +
                " <li>id="+member.getId()+"</li>\n" + // 동적 HTML
                " <li>username="+member.getUsername()+"</li>\n" + // 동적 HTML
                " <li>age="+member.getAge()+"</li>\n" + // 동적 HTML
                "</ul>\n" +
                "<a href=\"/index.html\">메인</a>\n" +
                "</body>\n" +
                "</html>");
    }
}
```

* 서블릿을 사용해 HTML 을 **동적으로** 만들어 응답했다.
  * `Member` 객체 값에 따라 HTML 이 동적으로 만들어진다.
* `서블릿` 👉 자바 코드로 HTML 을 만드는 것
  * 매우 복잡하고 비효율적이다.
* `템플릿 엔진` 👉 HTML 문서에 동적인 부분만 자바 코드로 작성하는 것
  * `JSP`, `Thymeleaf`

## JSP
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page import="hello.servlet.domain.member.Member" %>  <!-- java import 역할 -->
<%@ page import="hello.servlet.domain.member.MemberRepository" %> <!-- java import 역할 -->
<%
  MemberRepository memberRepository = MemberRepository.getInstance();
 
  String username = request.getParameter("username");
  int age = Integer.parseInt(request.getParameter("age"));
 
  Member member = new Member(username, age);
  memberRepository.save(member);
%>
<html>
<head>
    <title>Title</title>
</head>
<body>
<ul>
  <li>id=<%=member.getId()%></li>
  <li>username=<%=member.getUsername()%></li>
  <li>age=<%=member.getAge()%></li>
</ul>
</body>
</html>
```

* JSP 는 HTML 을 중심으로 **동적인** 부분만 자바 코드로 교체하였다.
  * JSP 는 서버 내부에서 서블릿으로 변한된다.
* `<%@ page contentType="text/html;charset=UTF-8" language="java" %>`
  👉 JSP 문서는 항상 이렇게 시작해야 한다.
* `<%@ page import="" %>`
  👉 자바의 import 에 해당한다.
* `<% ~~ %>`
  👉 자바 코드를 입력하는 부분이다.
* `<%= ~~ %>`
  👉 자바 코드를 출력하는 부분이다.

> **참고**
> JSP는 성능과 기능 측면에서 다른 템플릿 엔진에 밀렸다. 스프링과 잘 통합된 `Thymeleaf`를 사용하자.

## MVC 패턴
### 서블릿과 JSP 한계
![](https://velog.velcdn.com/images/pipiolo/post/13d8293d-254f-424a-9746-547dd34048c6/image.png)

* 너무 많은 역할을 담당하고 있다.
  * `서블릿`, `JSP` 중 하나의 기술로 비지니스 로직과 뷰 렌더링까지 처리하는 것은 너무 많은 역할을 담당하는 것이다.
  * 결과적으로 유지보수가 어려워진다.
* 변경 라이플 사이클이 다르다.
  * 비지니스 로직과 뷰 사이의 변경 사이클이 다르다.
  * 변경 사이클이 다른 것들을 하나의 코드로 관리하는 것은 좋지 않다.
* **핵심 비지니스 로직과 뷰 영역이 섞여 있다.**
  * 자바 코드와 HTML 코드가 섞여 있다.
  
### MVC 패턴 등장
![](https://velog.velcdn.com/images/pipiolo/post/e9836d1b-ed0e-4e29-b9f0-40c3f0dd208b/image.png)

![](https://velog.velcdn.com/images/pipiolo/post/0a19e51e-1b81-4071-91e7-8612c54d41a9/image.png)

* Model
  * `View` 에 출력할 데이터를 저장한다.
  * `Model` 덕분에 `View` 는 비지니스 로직이나 데이터 접근을 몰라도 된다.
* View
  * `Model` 에 저장된 데이터를 렌더링하는데 집중한다.
  * HTML 을 생성한다.
* Controller
  * HTTP 요청을 읽고 파라미터를 검증한다.
  * 비지니스 로직을 실행한다.
  * 결과 데이터를 `Model` 에 담아서 `View` 에게 전달한다.
* **기존에 섞여있던 비지니스 로직과 뷰 로직을 분리한다.**

> **MVC 패턴 2**
> 컨트롤러는 HTTP 요청∙응답에 집중한다. 비지니스 로직은 서비스 계층에서 별도로 처리한다. 컨트롤러 계층은 서비스 계층을 호출하는 역할을 담당한다.

### MVC 패턴 적용
```java
@WebServlet(name = "mvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members/save")
public class MvcMemberSavetServlet extends HttpServlet {

    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
    			throws ServletException, IOException {
                
        String username = request.getParameter("username");       
        int age = Integer.parseInt(request.getParameter("age"));
        
        Member member = new Member(username, age);
        memberRepository.save(member);
        
        request.setAttribute("member", member);
                
        String viewPath = "/WEB-INF/views/save.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```
```java
@WebServlet(name = "mvcMemberListServlet", urlPatterns = "/servlet-mvc/members")
public class MvcMemberListServlet extends HttpServlet {

    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
    			throws ServletException, IOException {
                
        List<Member> members = memberRepository.findAll();
        
        request.setAttribute("members", members);
                
        String viewPath = "/WEB-INF/views/members.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

* `request.setAttribute()` 👉 request 객체에 데이터를 보관해서 뷰에 전달한다.
  * 뷰는 `request.getAttribute()` 를 통해 데이터를 얻는다.
* `dispatcher.forward()` 👉 다른 서블릿이나 JSP로 이동한다. 서버 내부 호출이 일어난다.
* **MVC 패턴 덕분에 뷰와 컨트롤러의 역할을 구분지었다.**
  * 뷰는 JSP가, 컨트롤러는 서블릿이 담당한다.
  * 유지보수가 용이하다.

> **`/WEB-INF`**
> 이 경로 안에 있는 JSP는 외부에서 호출할 수 없다. 오직 컨트롤러를 통해서만 접근 가능하다.

> **Redirect vs Forward**
> 리다이렉트는 클라이언트에 응답이 갔다가 클라이언트가 다시 서버에 리다이렉트 경로로 요청한다. 클라이언트가 인지할 수 있고 URL도 변경된다.
> 반면에, 포워드는 서버 내부 호출이기 때문에 클라이언트가 인지할 수 없다.

### MVC 패턴 한계
* **공통 처리가 어렵다.**
  * 포워드 중복 👉 뷰로 이동하는 코드를 항상 호출해야 한다.
  * viewPath 중복
    * prefix : `/WEB-INF/views/`
    * suffix : `.jsp` 👉 특정 기술에 의존적이다.
  * 불필요한 의존
    * 사용 여부와 관계 없이 `HttpServletXXX` 객체를 파라미터로 받아야 한다.
* 컨트롤러에서 공통으로 처리해야 하는 부분이 많아진다.
  👉 수문장 역할을 하는 **프론트 컨트롤러(`Front Controller`)**를 도입한다.
  
---

# MVC 프레임워크
## 프론트 컨트롤러 패턴
![](https://velog.velcdn.com/images/pipiolo/post/607d2266-48b0-40cf-a3cd-c646ca7247d8/image.png)

* 공통 처리를 프론트 컨트롤러가 담당한다. **입구를 하나로!**
* 프론트 컨트롤러 서블릿만 클라이언트의 요청을 받는다.
  * 프론트 컨트롤러가 요청에 따라 알맞는 컨트롤러를 호출한다.
* 나머지 컨트롤러들은 서블릿에 의존할 필요 없다.

## MVC 프레임워크 만들기
![](https://velog.velcdn.com/images/pipiolo/post/d3a87ff6-e874-47ae-8297-df536e0ba805/image.png)

### 프론트 컨트롤러 도입
```java
@WebServlet(name = "frontController", urlPatterns = "/")
public class FrontController extends HttpServlet {

	private Map<String, Controller> controllerMap = new HashMap<>();
    
    public FrontController() {
    	controllerMap.put("/controller/memberes/save", new SaveController());
        controllerMap.put("/controller/memberes", new ListController());
    }

	@Override
    protected void service(HttpServletRequest request, HttpServletResponse response) {
    	String requestURL = request.getRequestURL();
        Controller controller = controllerMap.get(requestURL);
        
        controller.process(request, response);
    }
}
```

* `urlPatterns = "/"` 👉 모든 컨트롤러의 수문장 역할을 한다.
* `controllerMap`
  * key 👉 매핑 URL
  * value 👉 컨트롤러
* 컨트롤러 인터페이스(`Controller`)를 도입한다.
  * 프론트 컨트롤러는 컨트롤러 인터페이스에 의존한다.
  * 각 컨트롤러들은 인터페이스를 구현한다.
  * 프론트 컨트롤러는 다양한 컨트롤러 구현체들을 받을 수 있다.

### 뷰 도입
```java
public class MyView {
	
    private String viewPath;
    
    public void render(HttpServletRequest request, HttpServletResponse response) {
    	RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response); // JSP forword
    }
}
```

* `MyView` 객체를 도입한다.
* 뷰로 이동하는 중복 부분을 제거한다.
* 컨트롤러가 `MyView` 를 반환하면, 프론트 컨트롤러가 `render()` 를 실행한다.

### ModelView 도입
```java
public class SaveController implements Controller {

	public ModelView process(Map<String, String> paramMap) {
    	return new ModelView("save");
    }
}
```
```java
public class ModelView {
    private String viewName;
    private Map<String, Object> model = new HashMap<>();
}
```

* 서블릿 종속성 제거한다.
  * 기존 컨트롤러들은 서블릿에 종속적인 `HttpServletXXX` 를 사용했다.
  * 프론트 컨트롤러가 각 컨트롤러가 필요한 파라미터를 `paramMap` 객체에 담아서 호출한다.
* 뷰 이름 중복을 제거한다.
  * 이제 컨트롤러는 `ModelView` 객체를 반환한다.
  * 컨트롤러는 뷰의 논리 이름을 반환한다.
  * 실제 뷰의 물리 위치는 프론트 컨트롤러가 처리한다.
    * members 👉 `/WEB-INF/views/members.jsp` 
    * save 👉 `/WEB-INF/views/save.jsp`
* 뷰에서 필요한 데이터들은 `model`에 담는다.

### 뷰 리졸버 도입
```java
@WebServlet(name = "frontController", urlPatterns = "/")
public class FrontController {
    
  	private Map<String, Controller> controllerMap = new HashMap<>();
    
    public FrontController() {
    	controllerMap.put("/controller/memberes/save", new SaveController());
        controllerMap.put("/controller/memberes", new ListController());
    }
    
    @Override
    protected void service(HttpServletRequest reqeust, HttpServletResponse response) {
        String requestURL = request.getRequestURL();
        Controller controller = controllerMap.get(requestURL);
        
        Map<String, String> paramMap = createParamMap(request);
        ModelView mv = controller.process(paramMap);
        
        MyView view = viewResolver(mv.getViewName());
        view.render(mv.getModel(), reqeust, response);
    }
    
    private Map<String, String> createParamMap(HttpServletRequest reqeust) {
    	Map<String, String> paramMap = new HashMap<>();
        
        List<String> paramNames = reqeust.getParameterNames();
        for (String paramName : paramNames) {
        	paramMap.put(paramName, request.getParameter(paramName);
        }
        
        return paramMap;
    }
    
    private MyView viewResolver(String viewName) {
		return new MyView("/WEB-INF/views/" + viewName + ".jsp");
	}
}
```

![](https://velog.velcdn.com/images/pipiolo/post/4b60e6d5-f2d7-4f0f-a47d-806fb093e9e5/image.png)

* 컨트롤러가 반환한 뷰 논리 이름을 물리 뷰 경로로 변경한다.
* 뷰 경로(`prefix`), 뷰 확장명(`suffix`)이 변경되어도 프론트 컨트롤러만 변경하면 된다.
  * 기존에는 모든 컨트롤러에서 변경이 필요했다.

### 뷰 네임 반환
```java
public class SaveController implements Controller {
	
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
    	return "save";
    }
}
```

* 컨트롤러는 단순히 뷰 이름만 반환한다.
  * 기존에는 `ModelView` 를 반환했다.
* 대신에 `ModelView` 가 가지고 있던 `model`을 파라미터로 받는다.
  * 뷰에서 필요한 데이터들을 `model`에 담는다.

### 어댑터 패턴 도입
![](https://velog.velcdn.com/images/pipiolo/post/1dc3daf8-1fd5-4579-aa9c-4f55699e353b/image.png)

* 기존에는 한 가지 컨트롤러 인터페이스만 사용할 수 있었다.
  * 인터페이스에 정의된 매개변수, 반환 값만 사용할 수 있었다.
* 핸들러 어댑터 (`Handler Adapter`)
  * 다양한 방식의 컨트롤러 인터페이스를 처리할 수 있다.
  * `ModelView`, 뷰 이름 등 다양한 반환 값을 지원한다.
* 핸들러 (`Handler`)
  * 컨트롤러 확장한 개념
  * 컨트롤러 뿐만 아니라, 어댑터만 있다면 URL 매핑을 통해 어떤 것이라도 처리할 수 있다.
  
---

