# 1.4 HttpServlet

## HttpServlet의 특징
- 여태껏 HttpServlet을 상속하는 별도의 서블릿들을 정의하고 이를 이용하였다.
- HttpServlet은 GET/POST등에 맞게 doGet(), doPost()등을 제공하므로 개발자들은 본인에게 필요한 메소드를 Override하여 GET/POST 방식을 처리
- HttpServlet을 상속받은 클래스 객체는 Tomcat과 같은 WAS의 내부에서 자동으로 객체를 생성하고 관리하기 때문에 개발자가 객체 관리에 신경 쓸 필요가 없다.
- HttpServlet은 멀티 스레드에 의해서 동시에 실행될 수 있도록 처리되기 때문에 개발자가 동시에 많은 사용자를 어떻게 처리해야 하는지에 대한 고민도 할 필요가 없다.
- Servlet 클래스의 상속구조
    ```
    Servlet     Serializable        ServletConfig
        |------------|------------------|
                     |
                     |
               GenericServlet
                     |
                     |
                HttpServlet
                     |
                     |
           User defined Servlet
    ```
  - HttpServlet은 상위 클래스로 GenericServlet이라는 추상 클래스를 상속
  - GenericServlet은 HTTP가 아닌 요청과 응답을 의미하는 ServletRequest/ServletResponse라는 타입을 이용한다.
  - `일반적으로 개발자가 작성하는 servlet은 GenericServlet을 상속한 HttpServlet을 상속받아서 HTTP프로토콜에 특화된 기능들을 처리하기 위한 용도로 사용`

## HttpServlet의 라이프사이클
1. 브라우저가 톰캣에 서블릿이 처리해야 하는 특정한 경로를 호출
2. 톰캣은 해당 경로에 맞는 서블릿 클래스를 로딩하고 객체 생성, 이 과정에서 init()실행 (서블릿 클래스 로딩 대신 톰캣 실행 시 로딩하도록 하는 load-on-startup이라는 옵션도 있음.)
3. 생성된 서블릿 객체는 브라우저의 Request에 대한 정보를 분석해서 GET/POST 등의 정보와 함께 같이 전달되는 파라미터(쿼리 스트링의 내용)들을
HttpServletRequest라는 타입의 파라미터로 전달 받는다. 이 과정에서 응답을 처리하는데 필요한 기능들은 HttpServletResponse라는 타입의 객체로 전달 받는다.
4. 서블릿 내부에서는 GET/POST에 맞게 doGet()/doPost() 등의 메소드를 실행, 이 후 동일한 주소의 호출이 있을 떄 이전에 만들었던 객체 하나만을 이용하여 처리
5. 톰캣이 종료될 때는 서블릿의 destroy()라는 메소드 실행
> 서블릿의 객체는 경로에 맞게 하나만 만들어진다는 점과 매번 호출 시에는 자동으로 doGet()/doPost()를 이용해서 처리된다는 점이 제일 중요함
- 실제 동작을 좀 더 알아보기 위해 SampleServlet 클래스 작성
    ```java
    @WebServlet(name = "sampleServlet", urlPatterns = "/sample")
    public class SampleServlet extends HttpServlet {
        @Override
        public void init(ServletConfig config) throws ServletException {
            System.out.println("init(ServletConfig)........");
        }
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("doGet..."+this);
        }
        @Override
        public void destroy() {
            System.out.println("destory........");
        }
    }
    ```
    ```logcatfilter
    init(ServletConfig)........
    doGet...org.zerock.w1.SampleServlet@385ff7d2
    15-Feb-2024 12:15:41.786 INFO [Catalina-utility-2] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/Users/ds/ds_study/Resource/apache-tomcat-9.0.85/webapps/manager]
    15-Feb-2024 12:15:41.837 INFO [Catalina-utility-2] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/Users/ds/ds_study/Resource/apache-tomcat-9.0.85/webapps/manager] has finished in [50] ms
    doGet...org.zerock.w1.SampleServlet@385ff7d2
    doGet...org.zerock.w1.SampleServlet@385ff7d2
    doGet...org.zerock.w1.SampleServlet@385ff7d2
    /Users/ds/ds_study/Resource/apache-tomcat-9.0.85/bin/catalina.sh stop
    NOTE: Picked up JDK_JAVA_OPTIONS:  --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED
    15-Feb-2024 12:15:56.063 INFO [main] org.apache.catalina.core.StandardServer.await A valid shutdown command was received via the shutdown port. Stopping the Server instance.
    15-Feb-2024 12:15:56.063 INFO [main] org.apache.coyote.AbstractProtocol.pause Pausing ProtocolHandler ["http-nio-8080"]
    15-Feb-2024 12:15:56.065 INFO [main] org.apache.catalina.core.StandardService.stopInternal Stopping service [Catalina]
    15-Feb-2024 12:15:56.078 INFO [main] org.apache.coyote.AbstractProtocol.stop Stopping ProtocolHandler ["http-nio-8080"]
    destory........
    15-Feb-2024 12:15:56.089 INFO [main] org.apache.coyote.AbstractProtocol.destroy Destroying ProtocolHandler ["http-nio-8080"]
    Disconnected from server
    ```
  > /sample 호출시 init()와 doGet() 새로 고침 시 doGet()만 실행되며 동일한 객체로 실행, 톰캣 종료시 destroy()실행

## `HttpServletRequest`
 서블릿 객체에서 최종적으로 Request를 처리하는 doGet()/doPost() 등은 HttpServletRequest와 HttpServletResponse를 파라미터로 전달받는다.
 HttpServletRequest는 HTTP 메시지 형태로 들어오는 Request에 대한 정보를 파악하기 위해 제공되는데 주요 기능은 다음과 같다.

### HttpServletRequest의 주요 기능
|     기능     |                                   메소드                                    |                 설명                |
|:----------:|:------------------------------------------------------------------------:|:---------------------------------:|
| HTTP 헤더 관련 |                    GetHeaderNames()<br/>GetHeader(이름)                    |        HTTP 헤더 내용들을 찾아내는 기능       |
|   사용자 관련   |                            getRemoteAddress()                            |           접속한 사용자의 IP 주소          |
|   요청 관련    | getMethod()<br/>getRequestURL()<br/>getRequestURI()<br/>getServletPath() | GET/POST 정보, 사용자가 호출에 사용한 URL 정보 등 |
| 쿼리 스트링 관련  |     getParameter()<br/>getParameterValues()<br/>getParameterNames()      |    쿼리 스트링 등으로 전달되는 데이터를 추출하는 용도   |
|   쿠키 관련    |                               getCookies()                               |          브라우저가 전송한 쿠키 정보          |
|   전달 관련    |                          getRequestDispatcher()                          |                                   |
|   데이터 저장   |                              setAttribute()                              |    전달하기 전에 필요한 데이터를 저장하는 경우에 사용|

### getParameter()
- HttpServletRequest에서 가장 빈번하게 사용되는 메소드로 '?name=AAA&age=20'과 같은 쿼리 스트링에서 'name'이나 'age'라는 key를 이용해 value를 얻는 역할을 위해 사용
- 명심해야 하는 점은 getParameter()의 결과(return value)는 항상 `String`
- 해당 파라미터가 존재하지 않는다면 null 반환

### getParameterValues()
- 동일한 이름의 파라미터가 여러개 있는 경우 사용
- 예를 들어 name이라는 이름의 파라미터가 여러 개 존재한다면 getParameterValues()를 통해 `String[]`타입으로 변환

### setAttribute()
- JSP로 전달할 데이터를 추가할 때 사용
- Key, Value의 형태로 데이터를 저장
- Key는 문자열, Value는 모든 객체 타입 가능

### RequestDispatcher
- HttpServletRequest의 getRequestDispatcher()를 이용해 RequestDispatcher타입의 객체를 구한다.
- RequestDispatcher는 현재의 Request(요청)을 다른 서버의 자원(서블릿 or JSP)에게 전달하는 용도로 사용
- forward(), 와 include()가 있으며 실제 개발에서는 거의 forward()만 사용
  - forward() : 현재까지의 모든 응답(Response) 내용은 무시하고 JSP가 작성하는 내용만을 브라우저로 전달
  - include(): 지금까지 만들어진 응답(Response)내용 + JSP가 만든 내용을 브라우저로 전달

## `HttpServletResponse`
- `HttpServletRequest가 주로 '읽는' 기능을 제공한다면 HttpServletResponse는 반대로 '쓰는' 기능을 담당한다.`
- 웹 MVC 구조에서 HttpServletResponse는 JSP에서 주로 처리되기 때문에 서블릿 내에서 직접 사용되는 일은 많지 않고 주로 `sendRedirect()`를 이용하는 경우가 많다.


### HttpServletResponse의 주요 기능
|   기능    |       메소드        |               설명               |
|:-------:|:----------------:|:------------------------------:|
| MIME 타입 | setContentType() | 응답 데이터의 종류를 지정(이미지/html/xml 등) |
|  헤더 관련  |   setHeader()    |       특정 이름의 HTTP 헤더 지정        |
|  상태 관련  |   setStatus()    |  404, 200, 500 등 응답 상태 코드 지정   |
|  출력 관련  |   getWriter()    |  PrintWriter를 이용해서 응답 메시지 작성   |
|  쿠키 관련  |   addCookie()    |         응답 시에 특정 쿠키 추가         |
|  전달 관련  |  sendRedirect()  |          브라우저에 이동을 지시          |

### sendRedirect()
- 가장 많이 사용되는 메소드
- 브라우저에게 '다른 곳으로 가라'는 응답 메시지를 전달
- HTTP에서 `Location`이름의 HTTP Header로 전달된다.
- 브라우저는 `Location`이 있는 응답을 받으면 화면을 처리하는 대신에 주소창에 지정된 주소로 이동
- sendRedirect()를 사용하면 브라우저의 주소가 아예 변경되기 때문에 '새로고침'과 같은 요청을 미리 방지할 수 있고, 특정한 작업이 완전 히 끝나고 새로 시작하는 흐름을 만들 수 있다. `(PRG 패턴)`