# 1.2 웹 기본 동작 방식 이해하기

## GET, POST
- GET 방식
  - 주소창에 직접 원하는 데이터를 적거나 링크를 클릭해서 호출
  - 원하는 웹의 주소와 필요한 데이터를 '?' 와 '&,='를 이용해서 같이 전송하는 방식
  - 특정한 정볼르 조회하는 용도로 사용
- POST 방식
  - 입력 화면에서 필요한 내용을 작성한 후에 '전송'과 같은 버튼을 클릭해서 호출
  - `주소와 데이터를 따로 보내는 방식`
  - 보통 회원 가입이나 로그인 등의 처리가 이에 해당

|     |                                   Get                                   |                        Post                        |
|:---:|:-----------------------------------------------------------------------:|:--------------------------------------------------:|
| 주용도 |                                   조회                                    |                  등록/수정/삭제와 같은 처리                   |
| 구성  |                            URL뒤의 "?"와 쿼리 스트링                            |           URL 전달 후 <br/>HTTP Body로 쿼리스트링           |
| 효과  |                         사용자가 손쉽게 사용할 수 있는 링크 제공                         |               단순 조회가 아니라 원하는 작업을 처리                |
| 한계  | - 브라우저에 따라 길이의 제한 <br/>- URL뒤의 쿼리 스트링으로 모든 정보가 전달<br/>- 쿼리 스트링의 길이가 제한됨 | - GET 방식에 비해서 많은 양의 데이터를 전송<br/>- 주소창만으로는 테스트가 어려움 |

## Request, Response
- Request 요청 
  - 브라우저에서 서버에 앞선 방식으로 데이터를 요구하는 것을 Request
- Response 응답
  - 서버는 이에 대한 Response 데이터를 만들어 브라우저로 보낸다.
  - 서버에서는 브라우저로 Response를 보내는데 이를 `Static Data` 인지 `Dynamic Data`인지에 따라 다르게 처리한다.

## Static Data, Dynamic Data
- Static Data
  - 항상 동일하게 고정된 데이터를 전송하는 방식
  - 주로 HTML, CSS, 이미지 등의 데이터
- Dynamic Data
  - 매번 필요할 때마다 다른 데이터를 동적으로 구성해서 전송하는 방식
  - 예를 들어 메일함처럼 상황에 따라 동적으로 서버에서 데이터를 만들어 보내느 방식
  - 그래서 `Server Side Programming`이라고 한다.
- 항상 정적 데이터를 보내는 역할만 수행하는 서버는 `Web Server`
- 동적 데이터를 만들어 보내는 경우는 `Web Application Server (WAS)`라고 한다.
- 톰캣의 경우 엄밀하게 WAS로 보는 것이 좋지만, 대부분의 WAS는 웹 서버 기능도 같이 포함하므로 정적인 데이터 동적 데이터 모두 처리 가능

## HTTP
- 브라우저의 요청과 서버의 응답 사이에는 protocol이라는 한 가지 중요한 약속을 통해서 처리
- 웹에서는 HTTP : Hyper Text Trans-fer Protocol
- https://www.google.com 에서 https는 프로토콜 www.google.com 은 호스트(도메인)
- 개발자 도구에서 Network 탭을 이용하여 어떤 데이터들이 오고 가는지를 확인 가능
- `Connectionless` 비연결성 
  - 웹의 특성상 여러 명의 사용자가 브라우저를 통해 서버를 호출하는 구조
  - HTTP는 이를 위해 Connectionless 방식을 선택하여 하나의 요청과 응답을 처리한 후에 연결을 종료한다.
  - 웹 페이지의 새로운 결과를 다시 확인하기위해 '새로 고침'을 하는 것이 이것의 예
  - 서버는 하나의 Request를 빨리 처리하고 연결을 종료하여 다음 Request를 받아 적은 리소스를 이용해 많은 Request를 처리 가능

## Java's Server Side Programming
- 서버 사이드 프로그래밍 : 서버 쪽에서 프로그래밍을 통해 데이터를 처리할 수 있도록 구성하는 것
- 고려 사항
  1. 동시에 들어오는 여러 Request 어떻게 처리해야 할까 ?
  2. 서버에 문제 생겼을 때 어떻게 처리해야하는가 ?
  3. 어떤 방법으로 데이터 전송을 최적화할 수 있을까 ?
  4. 분산 환경 이나 분산 처리와 같은 문제들은 ?
- 자바의 경우 이러한 처리를 `JavaEE`라는 기술 스펙으로 정리
- `Servlet과 JSP는 JavaEE의 여러 기술 중에 하나`

## Servlet 기술
- Java EE 의 엄청나고 방대한 기술들 종류 중 가장 기본적인 기술이 `서블릿과 JSP`
- `Servlet 기술이란 서버에서 동적으로 요청과 응답을 처리할 수 있는 API들을 정의한 것`
- 서블릿을 지원하는 환경에서 개발자들은 서블릿에서 제공하는 API를 이용하여 코드를 작성
- 이러한 이유로 톰캣 설치 시에는 어떤 서블릿 버전을 지원하는지 확인
```agsl
Request : Browser -> Tomcat -> Servlet/JSP API 
Response : Servlet/JSP API -> Tomcat -> Browser
```
- 서블릿으로는 코드를 이용한 처리, JSP로는 화면 개발
- Servlet는 톰캣과 같이 서블릿을 실행할 수 있는 환경에서 실행이 가능한데 이를 `Servlet Container`라고 한다.
- Servlet 코드를 실행하는 주체는 Tomcat과 같은 서블릿 컨테이너이므로, 일반 자바 프로그램과 비교했을때 다음과 같은 점들이 달라진다.
  1. 객체를 생성하거나 호출하는 주체는 사용자가 아닌 서블릿 컨테이너
  2. 서블릿 클래스에서 생성하는 객체의 관리 자체가 서블릿 컨테이너에 의해서 관리
  3. 서블릿/JSP의 코드 개발은 기본적인 자바 API와 더불어 서블릿 API도 같이 사용해야 한다.
- HelloServlet의 코드를 살펴보면 import의 경우 javax로 시작하는 서블릿 관련 API를 사용
    ```java
    import javax.servlet.http.*;
    import javax.servlet.annotation.*;
    ```
- HelloServlet 클래스의 클래스 선언 자체가 HttpServlet이라는 부모 클래스가 지정되어 있다.
    ```java
    @WebServlet(name = "helloServlet", value = "/hello-servlet")
    public class HelloServlet extends HttpServlet{}
    ```
- 내부에는 init(), doGet(), destroy()가 작성되어 있는 이는 서블릿 API에서 지정된 메소드
    ```java
    public class HelloServlet extends HttpServlet {
        private String message;
    
        public void init() {
            message = "Hello World!";
        }
    
        public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
            response.setContentType("text/html");
    
            // Hello
            PrintWriter out = response.getWriter();
            out.println("<html><body>");
            out.println("<h1>" + message + "</h1>");
            out.println("<h1>" + "hello DS" + "</h1>");
            out.println("</body></html>");
        }
    
        public void destroy() {
        }
    }
    ```
- init(), doGet(), destroy()의 호출 주체는 개발자가 아닌 `서블릿 컨테이너`가 서블릿들을 관리하면서 호출된다.
- 이러한 메소드들은 일반적으로 Servlet의 `life cy-cle`라고 한다.

## JSP 기술
- Java Server Pages의 약자
- 서블릿 기술과 동일하게 서버에서 동적으로 데이터를 구성하는 기술
- JSP 기술은 서블릿과 달리 HTML 코드를 그대로 이용하고 필요할때 약간의 자바 코드를 넣는다.
    ```html
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <h1>Test JSP PAGE</h1>
    </body>
    </html>
    ```
- 반면 서브릿 코드는 자바 코드를 이용해 HTML문자열을 만들어내는 방식
- `JSP 코드는 자바 코드가 아님에도 서블릿과 동일하게 처리되는데 JSP 파일도 서블릿 코드로 변환되어서 컴파일 되고 실행되기 떄문이다.`
- `JSP 파일은 필요한 순간에 자바 파일로 생성되고, 이를 컴파일해서 class 파일로도 만들어 진다.`
- JSP는 서블릿 기술과 같은 목적이지만, 브라우저에 보내는 HTML 데이터를 만들어내는데 좀 더 특화된 기술
```html
<h1>NUM1 ${param.num1}</h1>
<h1>NUM2 ${param.num2}</h1>
```
- '${}'로 처리된 부분은 JSP에서 사용하는 EL(Expression Language)이라는 기술로, 서버에서 데이터를 출력하는 용도로 웹에서 System.out.println()과 유사한 역할을 한다고 생각하면된다.
- EL을 이용할 때는 param이라는 객체를 이용해 파라미터를 쉽게 추출 가능
- 이때 웹의 파라미터는 모두 문자열로 처리된다.
    ```html
    <h1>SUM ${Integer.parseInt(param.num1) + Integer.parseInt(param.num2)}</h1>
    ```
   > 따라서 결과를 처리하기 위해 Integer.parseInt()를 이용하여 처리

## JSP의 올바른 사용법
- JSP는 기본적으로 GET/POST 방식의 호출을 구분하지 않음
- POST 방식으로 접근해야 하는 JSP의 경우 GET방식으로도 호출할 수 있고 이떄 Integer.parseInt와 같은 부분에서 문제가 발생한다.
- 이러한 이유로 최근에 JSP는 다음과 같은 제한적인 용도로 사용
  1. `JSP에서 쿼리 스트링이나 파라미터를 처리하지 않는다. JSP대신 서블릿을 통해서 처리`
  2. `JSP는 입력 화면을 구성하거나 처리 결과를 보여주는 용도`
  3. `브라우저는 직접 JSP 경로를 호출하지 않고 서블릿 경로를 통해서 JSP를 보는 방식으로 사용`
> 이러한 문제를 해결하기 위해서 등장한 방식이 `Web MVC`방식으로 JSP는 결과만 출력하고, 처리는 Servlet을 이용하는 방식으로 변화

## 요약
- Servlet/JSP 모두 Java EE 스펙의 일부
- Servlet/JSP를 실행하기 위해서는 Servlet Container가 필요함
- Servlet Container가 Servlet/JSP 객체를 생성하고 Life cycle를 관리함
- JSP는 내부적으로 서블릿과 같은 방식의 코드로 변환됨
- JSP는 HTML 내에 자바 코드를 추가하는 방식이고, Servlet 방식은 자바 코드 안에 HTML 코드를 추가하는 방식
