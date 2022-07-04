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

# 스프링 MVC 구조 이해
![](https://velog.velcdn.com/images/pipiolo/post/1991c942-de5a-41b8-a2ac-c809d7844aed/image.png)

* MVC 프레임워크 👉 스프링 MVC
  * FrontController 👉 Dispatcher Servlet
  * handlerMappingMap 👉 HandlerMapping
  * MyHandlerAdapter 👉 HandlerAdapter
  * ModelView 👉 ModelAndView
  * MyView 👉 View
  * viewResolver 👉 ViewResolver

## DispatcherServlet
* 디스패처 서블릿(`Dispatcher Servlet`)은 스프링 MVC의 프론트 컨트롤러이다.
* `HttpServlet` 을 상속하고 모든 경로(`urlPatterns="/"`)에 대해서 매핑한다.
  * `DispatcherServlet.doDispatch()` 가 호출된다.

### 동작 순서
* 핸들러 조회 👉 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
* 핸들러 어댑터 조회 👉 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
* 핸들러 어댑터 실행
* 핸들러 실행
* ModelAndView 반환 👉 핸들러 어댑터는 핸들러 반환 값을 ModelAndView 로 변환해서 반환한다.
* ViewResolver 호출 👉 뷰 리졸버를 찾고 실행한다.
* View 반환 👉 뷰 리졸버는 논리 이름을 물리 이름으로 바꾸고 View 객체로 반환한다.
* 렌더링 👉 View 객체를 통해서 렌더링한다.
  
## HandlerMapping & HandlerAdapter

### HandlerMapping (핸들러 매핑)
* 핸들러 매핑에서 핸들러(컨트롤러)를 찾을 수 있어야 한다.
* `RequestMappingHandlerMapping` 👉 애노테이션 기반의 컨트롤러인 `@RequestMapping`에서 사용한다.
* `BeanNameUrlHandlerMapping` 👉 스프링 빈의 이름으로 핸들러를 찾는다.

### HandlerAdapter (핸들러 어댑터)
* 핸들러를 실행할 수 있는 핸들러 어댑터가 필요하다.
* `RequestMappingHandlerAdapter` 👉 애노테이션 기반의 컨트롤러인 `@RequestMapping`에서 사용한다
* `HttpRequestHandlerAdapter` 👉 HttpRequestHandler 처리한다.
* `SimpleControllerHandlerAdapter` 👉 Controller 인터페이스(애노테이션X, 과거에 사용) 처리한다.
 
> **@RequestMapping**
> 가장 우선 순위가 높은 핸들러 매핑과 어댑터는 `RequestMappingHandlerXXX` 이다. `@RequestMapping` 애노테이션 기반의 컨트롤러를 지원하는 매핑과 어댑터이다.

## ViewResolver
* 컨트롤러가 반환한 뷰 논리 이름을 물리 뷰 경로로 변경한다.
* `BeanNameViewResolver` 👉 빈 이름으로 뷰를 찾아서 반환한다.
* `InternalResourceViewResolver` 👉 JSP를 처리할 수 있는 뷰를 반환한다.
* `ThymeleafViewResolver` 👉 타임리프 뷰 템플릿을 처리할 수 있는 뷰를 반환한다.

> **`application.properties`**
> ```
> spring.mvc.view.prefix = /WEB-INF/views/
> spring.mvc.view.suffix = .jps
> ```
> 스프링 부트는 `InternalResourceViewResolver` 를 자동 등록하는데, 이 때 `application.properties` 에 등록한 설정 정보를 사용한다.

## 스프링 MVC
```java
@Controller
@RequestMapping("members")
public class MemberController {

	private final MemberRepository memberRepository;

	@GetMapping("/add-form")
    public String addForm() {
    	return "addForm";
    }
    
    @PostMapping("/save")
    public String save(@RequestParam("username") String username, 
                       @RequestParam int age,
                       Model model
    ) {
    	Member member = new Member(username, age);
    	memberRepository.save(member);
        
        model.addAttribute("member", member);
        return "save";
    }
    
    @GetMapping
    public ModelAndView members() {
    	List<Member> members = memberRepository.findAll();
        
        ModelAndView mv = new ModelAndView("members"):
        mv.addObject("members", members);
        
        return mv;
    ]
}
```

### @RequestMapping

* `@Controller`
  * 자동으로 스프링 빈으로 등록된다. 내부에 `@Component` 가 있다.
  * 스프링 MVC에서 애노테이션 기반 컨트롤러로 인식된다.
* `@RequestMapping`
  * 요청 정보를 매핑한다.
    * 해당 URL이 호출되면 메서드를 호출한다.
  * 애노테이션 기반 컨트롤러를 지원한다.
    * `RequestMappingHandlerMapping`∙`RequestMappingHandlerAdpater` 이 사용된다.
  * 실무에서 99.9% 사용되는 방식이다.
* `@RequestMapping` 👉 `@GetMapping`∙`@PostMapping`
  * URL 매칭 뿐만 아니라, HTTP Method 도 함께 구분할 수 있다.
  * `@RequestMapping(value = "add-form", method = RequestMethod.GET)` 와 동일히다.
  * GET, POST, PUT, DELETE, PATCH 모두 사용 가능하다.
    * `@RequestMapping` 은 모든 HTTP Method 접근 허용한다.
* `@RequestParam`
  * HTTP 요청 파라미터를 `@RequestParam` 으로 받을 수 있다.
  * `@RequestParam("username")` 은 `request.getParameter("username")` 와 거의 동일하다.
  * 마찬가지로 GET 쿼리 파라미터, POST Form 방식 모두 지원한다.

> **`RequestMappingHandlerMapping`**
> `@Controller` 또는 `@RequestMapping` 이 클래스 레벨이 붙은 경우 매핑 정보를 인식한다.

### Model
* `ModelAndView`
  * 모델과 뷰 정보를 담아서 반환한다.
  * 일반적으로 `Model`∙`ViewName` 으로 사용한다.
* `Model`
  * `Model` 을 파라미터로 받을 수 있다.
  * 뷰에 필요한 데이터를 입력할 수 있다.
* `ViewName`
  * 뷰의 논리 이름을 문자로 반환한다.
  * 뷰 리졸버를 통해 뷰의 물리 경로로 변환된다.
  
--- 

# 스프링 MVC 이해
```java
@RequestMapping("/mapping")
@RestController
public class MappingController {
	
    private final Logger log = LoggerFactory.getLogger(getClass());
    
    @GetMapping("/users/{userId}/orders/{orderId}")
    public String mappingPath(@PathVariable("userId") String data, @PathVariable Long orderId) {
    	log.info("userId = {}, orderId={}", data, orderId);
        return "OK";
    }
}
```

* HTTP API 는 URL 경로에 식별자를 넣는 스타일을 선호한다.
* `@PathVariable`
  * URL 경로를 템플릿화 할 수 있다.
  * `@PathVariable` 이름과 파라미터 이름이 같으면 생략할 수 있다.
  
> **다른 URL → 같은 매핑**
> 다음 두 요청은 다른 URL 이지만, 스프링은 같은 요청으로 매핑한다.
> * URL 요청 → `/hello`, `/hello/`
> * 매핑 → `/hello`

## HTTP 헤더
```java
@Slf4j
@RestController
public class RequestHeaderController {

	@RequestMapping("/headers")
    public String headers(HttpServletRequest request, HttpServletResponse response,
                          HttpMethod httpMethod,
                          Locale locale,
                          @RequestHeader MultiValueMap<String, String> headerMap,
                          @RequestHeader("host") String host,
                          @CookieValue(value = "myCookie", required = false) String coockie
    ) {
		return "OK";
    }                     
}
```

* `HttpMethod` 👉 HTTP 메서드를 조회한다.
* `Locale` 👉 Locale 정보를 조회한다.
* `@RequestHeader MultiValueMap<String, String>` 👉 모든 HTTP 헤더를 MultiValueMap 형식으로 조회한다.
* `@RequestHeader("host") String host` 👉 특정 HTTP 헤더를 조회한다.
  * 속성
    * `required` → 필수 값 여부
    * `defaultValue` → 기본 값 지정
* `@CookieValue(value = "myCookie", required = false) String cookie` 👉 특정 쿠기를 조회한다.
  * 속성
    * `required` → 필수 값 여부
    * `defaultValue` → 기본 값 지정

> **`MultiValueMap`**
> 하나의 키에 여러 값을 받을 수 있는 `Map` 이다.
> ```java
> MultiValueMap<String, String> map = new LinkedMultiValueMap<>();
> map.add("key", "value1");
> map.add("key", "value2");
>
> List<> values = map.get("key");
> ```
  
> **`@Slf4j`**
> * `private static final Logger log = LoggerFactory.getLogger(getClass());`
> * 코도를 자동으로 생성해서 로그를 선언해준다.
>   * 실제 생성되는 코드는 패키지 명까지 포함한다.
> * 개발자는 `log` 를 사용하면 된다.
  
## HTTP 요청
* GET 쿼리 파라미터∙POST HTML Form
  * 형식이 같으므로 구분없이 조회할 수 있다.
  * 요청 파라미터(`Request Parameter`) 조회라 한다.
* HTTP Message Body
  * HTTP API 에서 사용된다.

### GET 쿼리 파라미터∙POST HTML Form
#### @RequestParam
```java
@ResponseBody
@Controller
public class RequestParamController {
	
    @RequestMapping("/request-param")
    public String requestParam(@RequestParam("username") String hello, int age) {
    	return "OK";
    }
}
```

* `@ResponseBody` 👉 반환 값을 뷰 조회로 사용하지 않고 HTTP Message Body 에 입력한다.
  * HTTP 응답에 사용된다. HTTP 요청과 독립적으로 작동한다.
* `@RequestParam` 👉 파라미터 이름으로 바인딩한다.
  * `@RequestParam("username") String hello` → `request.getParameter("username")`
  * HTTP 파라미터와 이름과 매개 변수 이름이 같으면 생략 가능하다.
    * `@RequestParam("age") int age` → `@RequestParam int age`
  * `String`, `int` 등 단순 타입이면 `@RequestParam` 생략 가능하다.
    * `@RequestParam int age` → `int age`
  * `Map`, `MultiValueMap` 을 통해 값을 받을 수 있다.
* **`GET 쿼리 파라미터`∙`POST HTML Form` 를 받을 때 사용한다.**

> **`@RequestParam(required)`**
> * 파라미터 필수 여부를 지정한다. 기본 값은 필수(`true`) 이다.
> * `/request-param`
>   * `username` 이 없으므로 400 예외가 발생한다.
> * `/request-param?username=`
>   * 파라미터 이름만 있고 값이 없는 경우 빈 문자로 통과된다. `String` 은 NULL 받을 수 있다.
> * `/request-param` + `@RequestParam(required = false) int age`
>   * `int`, `long` 등 기본 형에 NULL 입력할 수 없다. 500 예외가 발생한다.
>   * NULL 을 받을 수 있는 `Integer` 로 변경하거나 `defaultValue` 옵션을 사용해야 한다.
> * `@RequestParam(required = false, defaultValue = -1) int age`
>   * 파라미터가 없는 경우, 빈 문자(`NULL`)인 경우 모두 `defaultValue` 값이 적용된다.

#### @ModelAttribute 
```java
@Controller
public class RequestParamController {

	@RequestMapping("/model-attribute")
	public String modelAttribute(@ModelAttribute HelloData data) {
    
    }
}
```

* 객체 데이터를 받을 때 사용한다.
  * 객체를 생성하고 `setter` 메소드를 통해 바인딩한다.
  * 바인딩 오류가 발생하면, `BindException` 예외가 발생한다.
* `@ModelAttribute` 는 생략할 수 있다.
* **`GET 쿼리 파라미터`∙`POST HTML Form` 를 받을 때 사용한다.**

> **참고**
> `@RequestParam`∙`@ModelAttribute` 생략할 경우 다음과 같이 동작한다.
> `@RequestParam` 👉 `String`, `int`, `Long` 같은 단순 타입
> `@ModelAttribute` 👉 `ArgumentResolver` 로 지정해둔 타팁 외 나머지

### HTTP Message Body
#### @RequestBody - 단순 텍스트
```java
@Controller
public class RequestBodyStringController {

	@PostMapping("/request-body-string-v1)
    public void requestBodyStringV1(HttpServletRequest request, HttpServletRespons response) {
    	InputStream input = request.getInputStreama();
    	String messageBody = StreamUtils.copyToString(input, UTF_8);
        
        response.getWriter().write("OK");
    }

	@PostMapping("/request-body-string-v2)
    public void requestBodyStringV2(InputStream input, Writer responseWriter) {
    	String messageBody = StreamUtils.copyToString(input, UTF_8);
        
        responseWriter.write("OK");
    }
    
    @PostMapping("/request-body-string-v3)
    public HttpEntity<String> requestBodyStringV3(HttpEntity<String> httpEntity) {
    	String messageBody = httpEntity.getBody();
        
        return new HttpEntity<>("OK");
    }
    
    @ResponseBody
    @PostMapping("/request-body-string-v4)
    public String requestBodyStringV4(@RequestBody String messageBody) {
    	return "OK";
    }
}
```

* `HttpServletXXX` 을 매개 변수로 받아, `request.getInputStrem()`∙`response.getWriter()` 를 통해 HTTP 요청∙응답에 접근한다.
* `InputStream`∙`OutputStream` 를 파라미터로 받을 수 있다.
* `HttpEntity` 👉 HTTP header, body 정보를 조회한다.
  * 메세지 바디 정보를 조회하거나 입력할 수 있다.
    * 응답에도 사용 가능하다.
  * `RequestEntity`∙`ResponseEntity` 는 `HttpEntity` 를 상속해서 각각 요청∙응답에 사용된다.
* `RequestEntity` 👉 HTTP API 요청에서 사용된다.
  * HTTP Method, URL 정보가 추가되었다.
  * `RequestEntity` → `@RequestHeader` + `@RequestBody`
* `ResponseEntity` 👉 HTTP API 응답에서 사용된다. 
  * HTTP 상태 코드 설정 가능하다.
  * `ResponseEntity` → `@ResponseHeader` + `@ResponseBody`
* HTTP 메시지 바디를 조회한다.
* 요청 파라미터 조회 기능과 관련 없다. `@RequestParam` ❌, `@ModelAttribute` ❌

> **요청 파라미터 vs HTTP 요청 메시지 바디**
> * 요청 파라미터 조회 👉 `@RequestParam`, `@ModelAttribute`
> * HTTP 요청 메시지 바디 조회 👉 `@RequestBody`

> **`@ResponseBody`**
> HTTP 메시지 바디에 데이터를 입력할 수 있다. 반환 값을 뷰 이름으로 사용하지 않는다.

> **참고**
> 스프링 MVC 는 **HTTP 메시지 컨버터(`HttpMessageConverter`)** 기능을 사용해서 HTTP 메시지 바디를 읽어 문자나 객체로 변환해서 전달한다.

#### @RequestBody - JSON
```java
@Controller
public class RequestBodyJsonController {

	private ObjectMapper objectMapper = new ObjectMapper();
    
    @PostMapping("/request-body-json-v1")
    public void requestBodyJsonV1(HttpServletRequest request, HttpServletResponse response) {
    	InputStream input = request.getInputStreama();
    	String messageBody = StreamUtils.copyToString(input, UTF_8);
        
        HelloData data = objectMapper.readValue(messageBody, HelloData.class);
        
        response.getWriter().write("OK");
    }
    
    @ResponseBody
    @PostMapping("/request-body-json-v2")
    public String requestBodyJsonV2(@RequestBody String messageBody) {
        HelloData data = objectMapper.readValue(messageBody, HelloData.class);
        
        return "OK";
    }
    
    @PostMapping("/request-body-json-v3")
    public HttpEntity<String> requestBodyJsonV3(HttpEntity<HelloData> httpEntity) {
        HelloData data = httpEntity.getBody();
        
        return new HttpEntity<>("OK");
    }
    
    @ResponseBody
    @PostMapping("/request-body-json-v4")
    public String requestBodyJsonV4(@RequestBody HelloData data) {
        return "OK";
    }
}
```

* Jackson 라이브러리 `ObjectMapper` 를 사용해서 messageBody 를 객체로 변환한다.
* `@RequesyBody` 를 사용해 messageBody 를 매개 변수로 받아 객체로 변환한다.
  * `objectMapper` 를 이용해 json 으로 변환하는 과정은 똑같이 불편하다.
* `HttpEntity<HelloData> httpEntity` → 객체 타입으로 조회할 수 있다.
* `@RequestBody HelloData data` → `@RequestBody` 에 객체를 지정할 수 있다.
  * `HttpEntity` 혹은 `@RequestBody` 를 사용하면 HTTP 메시지 컨버터가 문자, 객체, JSON → 객체 변환해준다.
* `@RequestBody` 는 생략 불가능하다.
  * `@RequestBody` 를 생략하면 `@RequestParam` 혹은 `@ModelAttribute` 가 동작한다.
    * `String`, `int` 등 단순 타입 → `@RequestParam`
    * 나머지 → `@ModelAttribute`

> **주의**
> HTTP 요청 content-type : application/json 인 경우에만 JSON → 객체 변환을 처리하는 HTTP 메시지 컨버터가 동작한다.

> **참고**
> `@ResponseBody` 를 사용하면, 반환 값이 객체 일때 JSON 변환이 이루어진다.
> * `@RequestBody` 👉 JSON → HTTP 메시지 컨버터 → 객체
> * `@ResponseBody` 👉 객체 → HTTP 메시지 컨버터 → JSON

## HTTP 응답
* 정적 리소스
  * HTML, CSS, JS 을 제공한다.
* 뷰 템플릿 사용
  * 동적인 HTML 을 제공할 때, 뷰 팀플릿을 사용한다.
* HTTP 메시지 사용
  * HTTP API 를 제공할 때, HTTP 메시지 바디에 JSON 형식으로 데이터를 전송한다.
  
### 정적 리소스
* **정적 리소스는 해당 파일을 변경 없이 그대로 전송한다.**
* 스프링 부트는 다음 디렉토리에 이는 정적 리소스를 제공한다.
  * `/static`, `/public`, `/resources`, `/META-INF/resources`
* `src/main/resource` 는 리소스를 보관하는 곳이다.
  * `src/main/resources/static/hello.html` → `http://localhost:8080/hello.html`
  
### 뷰 템플릿
```java
@Controller
public class ResponseViewController {

	@RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {
    	ModelAndView mv = new ModelAndView("hello");
        mv.addObject("data", "Hello!");
        
        return mv;
    }
    
    @RequestMapping("/response-view-v2")
    public String responseViewV2(Model model) {
    	model.addAttribute("data", "Hello!");

        return "hello";
    }
    
    @RequestMapping("/response-view-v3")
    public void responseViewV3(Model model) {
    	model.addAttribute("data", "Hello!");
    }
}
```

* `String` 반환 값
  * `@ResponseBody` 가 없으면, 뷰 지졸버가 실행되어서 `hello` 뷰를 찾고 렌더링 한다.
  * `@ResponseBody` 가 있으면, HTTP 메시지 바디에 `hello` 문자가 입력된다.
* `Void` 반환 값
  * `@Controller` 를 사용하고 HTTP 메시지 바디를 처리하는 파라미터가 없으면 요청 URL을 참고해서 논리 뷰 이름으로 반환한다.
  * 명시성이 너무 떨어진다. 사용하지 말자.
  
> **`ThymeleafViewResolver`**
> `Thymeleaf` 라이브러리를 추가하면, 스프링 부트가 `ThymeleafViewResolver` 를 자동으로 스프링 빈으로 등록한다. 
> `ThymeleafViewResolver` 은 다음과 같은 설정을 기본 값으로 가진다.
>
> `spring.thymeleaf.prefix=classpath:/templates/`
> `spring.thymeleaf.suffix=.html`

### HTTP API
```java
@RestController
public class ResponseBodyController {
	
    @GetMapping(/response-body-string-v1)
    public void responseBodyV1(HttpServletResponse response) {
    	response.getWriter().write("OK");
    }
    
    @GetMapping(/response-body-string-v2)
    public ResponseEntity<String> responseBodyV2() {
    	return new ResponseEntity<>("OK", HttpStatus.OK);
    }
    
    @ResponseBody
    @GetMapping(/response-body-string-v3)
    public String responseBodyV3() {
    	return "OK";
    }
    
    @GetMapping("/response-body-json-v1")
 	public ResponseEntity<HelloData> responseBodyJsonV1() {
 		HelloData data = new HelloData();
 		data.setUsername("userA");
 		data.setAge(20);
        
 		return new ResponseEntity<>(data, HttpStatus.OK);
 	}
    
    @ResponseStatus(HttpStatus.OK)
 	@ResponseBody
 	@GetMapping("/response-body-json-v2")
 	public HelloData responseBodyJsonV2() {
 		HelloData data = new HelloData();
 		data.setUsername("userA");
 		data.setAge(20);
 
 		return data;
 	}
}
```

* `@RestController` 👉 `@Controller` + `@ResponseBody`
  * 해당 클래스의 모든 메소드에 `@ResponseBody` 가 적용된다.
  * HTTP 메시지 바디에 데이터를 입력한다.
  * HTTP API(`REST API`)를 만들 때 사용한다.
  
> **참고**
> 뷰 템플릿을 사용해도, 결국에는 HTTP 응답 메시지 바디에 HTML 데이터가 입력되어 전달된다. 
> 여기서 설명하는 것은 정적 리소스나 뷰 템플릿을 거치치 않고, 직접 HTTP 응답 메시지 바디에 데이터를 입력하는 것을 말한다.

## HTTP 메시지 컨버터
![](https://velog.velcdn.com/images/pipiolo/post/6d1b689e-0f74-45e8-b121-98524315f319/image.png)

* HTTP API 처럼 JSON 데이터를 HTTP 메시지 바디에 직접 읽거나 쓰는 경우 사용한다.
* 스프링 MVC 는 다음의 경우에 HTTP 메시지 컨버터를 적용한다.
  * HTTP 요청 : `@RequestBody`, `RequestEntity`, `HttpEntitiy`
  * HTTP 응답 : `@ResponseBody`, `ResponseEntity`, `HttpEntitiy`
* `@ResponseBody`
  * HTTP의 메시지 바디에 문자 내용을 직접 입력한다.
  * `ViewResolver` 대신에 `HttpMessageConverter` 가 동작한다.
    * `StringHttpMessageConverter` 👉 문자 처리
    * `MappingJackson2HttpMessageConverter` 👉 객체 JSON 처리
* HTTP 메시지 컨버터는 HTTP 요청∙응답 모두 사용된다.
* `ByteArrayHttpMessageConverter` 👉 byte[]
* `StringHttpMessageConverter` 👉 String
* `MappingJackson2HttpMessageConverter` 👉 application/json

### RequestMappingHandlerAdapter 구조
![](https://velog.velcdn.com/images/pipiolo/post/0fc9fd8f-4c79-472a-8f7d-dfa363ce495f/image.png)

* `RequestMappingHandlerAdapter` 는 `ArgumentResolver` 를 호출해서 컨트롤러가 필요하는 다양한 파라미터를 생성한다.
  * `ArgumentResolver` 덕분에 애노테이션 기반 컨트롤러는 매우 다양한 파라미터를 유연하게 받을 수 있다.
* `ReturnValueHandler` 는 컨트롤러 반환 값을 변환하고 처리한다.
  * 애노테이션 기반 컨트롤러는 `ModelAndView`, `String`, `@ResponseBody` 등 다양한 값을 반환할 수 있다.
* `HttpMessageConverter`
  * HTTP 요청(`@RequestBody`, `HttpEntity`) 를 처리하는 `ArgumentResolver` 가 HTTP 메시지 컨버터를 호출해 컨트롤러가 필요로 하는 파라미터를 생성한다.
  * HTTP 응답(`@ResponseBody`, `HttpEntity`) 를 처리하는 `ReturnValueHandler` 가 HTTP 메시지 컨버터를 호출해 반환 값을 만든다.

---

