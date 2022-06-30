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
