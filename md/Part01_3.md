# 1.3 Web MVC 방식

## MVC 구조와 서블릿/JSP
- 서블릿 코드 : 자바 코드를 그대로 이용 가능, 상속이나 인터페이스의 처리도 가능
- JSP : HTML 코드를 바로 사용할 수 있으므로 HTTP 메시지 작성에는 적합
- 이를 절충하여 브라우저의 Request는 해당 주소를 처리하는 `서블릿`에 전달,
- `Servlet은 준비한 데이터들을 JSP로 전달하고 JSP에서는 EL을 이용해서 최종적인 결과 데이터 생성`
- Request -> Servlet
  - Response에 필요한 데이터 완성
  - 다른 객체들 연동 협업 처리
  - 상속이나 인터페이스의 활용
  - 코드의 재사용
- Servlet -> JSP -> Response
  - EL을 이용하여 데이터 출력
  - HTML Code활용
  - 브라우저로 전송할 최종 결과 완성
- JSP를 이용해서 생성된 결과 화면은 톰캣을 통해서 브라우저로 전송
- Web MVC라는 구조는 `Model - View - Controller`의 역할을 분리해서 처리하는 구조
- 데이터는 컨트롤러에서 결과는 뷰에서 처리
- 서블릿이 바로 컨트롤러, JSP가 뷰 역할
- 서블릿은 JSP에 필요한 데이터를 가공하는 역할을 하고 이때 필요한 데이터를 제공하는 객체를 `MODEL`
```
Request -> Controller <- Model
                |
Response <-    View 
```

## MVC 구조로 다시 설계하는 계산기
- 다음과 같은 원칙들을 명심해서 작업 진행
  1. 브라우저의 호출은 반드시 컨트롤러의 역할을 하는 서블릿을 호출
  2. JSP는 브라우저에서 직접 호출하지 않고 Controller를 통해서만 접근하도록 구성 -> `jsp파일을 WEB-INF 경로 밑으로 이동시켜 브라우저에서 직접 접근이 불가능하게 한다.`
- GET 입력 화면의 설계
  1. 브라우저는 /input과 같이 특정한 주소를 호출
  2. /input에 맞는 서블릿을 InputController로 작성, GET 방식일때만 동작하도록 구성
  3. InputController의 화면 처리는 input.jsp를 이용하도록 지정
  4. input.jsp에는 HTML 코드를 이용해 브라우저에서 볼 수 있는 결과를 생성
- POST 처리의 설계
  1. input.jsp의 <form> 태그의 action을 '/calcResult'와 같이 변경하고 이에 해당하는 CalcResultServlet 컨트롤러를 생성
  2. CalcResultServlet는 <form>으로 전달되는 Parameter의 데이터 값을 읽어내서 결과 데이터를 만들어내야 함
  3. 만들어진 결과를 calcResult.jsp로 전달해야하고 JSP는 EL을 사용하여 결과 데이터를 출력

## RequestDispatcher 이용한 요청 배포
```java
@WebServlet(name = "inputController", urlPatterns = "/calc/input")
public class InputController extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("InputController...doGet...");

        RequestDispatcher dispatcher = req.getRequestDispatcher("/WEB-INF/calc/input.jsp");

        dispatcher.forward(req,resp);
    }
}
```
- InputController의 가장 핵심적인 코드는 RequestDispatcher객체를 이용하여 forward()를 실행하는 부분
- 서블릿에 전달된 요청(Request)를 다른쪽으로 전달 혹은 배포 하는 역할을 하는 객체
- Browser -> InputController -> /WEB-INF/calc/input.jsp
  - 브라우저가 호출하는 경로는 /calc/input
  - 내부적으로는 input.jsp에게 배포
  - 실제 결과물을 만드는 뷰 -> input.jsp
- 서버가 실행된 로그를 보면 'InputController... doGet..' 메세지가 출력된 것으로 보아 중간에 서블릿이 실행됨

## POST 방식을 통한 처리 요청
```java
@WebServlet(name = "calcController", urlPatterns = "/calc/makeResult")
public class CalcController extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String num1 = req.getParameter("num1");
        String num2 = req.getParameter("num2");

        System.out.printf("num1 : %s\n", num1);
        System.out.printf("num2 : %s", num2);
        
    }
}
```
- 브라우저에서 POST방식으로 호출하는 경우에만 호출 가능
- <form> 태그의 action을 '/calc/makeResult'로 변경해야함

## sendRedirect()
- doPost()에 resp.sendRedirect("/index") 추가
- PRG 패턴 (Post - Redirect - GET)
- 웹 MVC 구조에서 가장 흔하게 사용하는 패턴
  1. Browser : 사용자는 컨트롤러에세 원하는 작업을 POST 방식으로 처리하기를 요청 - POST
  2. Controller A : POST 방식을 컨트롤러에서 처리하고 브라우저는 다른 경로로 이동(GET)하라는 응답 - Redirect
  3. Browser: 브라우저는 이동해야 하는 주소를 받음
  4. Browser: 브라우저는 GET방식으로 이동(서버를 호출) - GET
  5. Controller B : GET 방식의 데이터를 처리하고 Response 전송 
  6. Browser : Response수신
- PRG 패턴은 POST 방식의 처리 후에 바로 다른 주소로 브라우저가 이동하기 때문에 반복적으로 POST 호출이 되는 상황도 막을 수 있고, 
사용자의 입장에서도 처리가 끝나고 다시 처음 단계로 돌아간다는 느낌을 주게 된다.

## Todo 웹 App 와이어 프레임 그리기

### 작성 요령
1. 화면에는 해당 페이지를 볼 수 있는 경로(URL) 명시
2. GET 방식으로 동작하고 눈에 보이는 것을 가장 먼저 구상
3. POST 방식으로 처리되는 컨트롤러는 다른 그림으로 표현
4. Redirect되어 보여지는 경우는 다른 선으로 표시

### 목록 화면
- URL : /todo/list
- method : GET
- 가장 먼저 시작하는 화면으로 List를 나타내주는 page

### 등록 화면
- URL : /todo/register
- method : GET
- todo list를 등록하기 위한 page

### 등록 화면
- URL : /todo/register
- method : POST
- GET을 통해 나타난 page에서 REGISTER버튼을 클릭하면 POST방식으로 요청
  - PRG 패턴
  1. /todo/register (POST) 
  2. TodoRegisterController doPost() 
  3. /todo/list (Redirect)
  4. /todo/list (GET)

### 조회 화면
- URL : /todo/read
- method : GET
- 목록 화면에서 특정한 Todo 글 번호를 선택하면 동작하도록 설계

### 수정/삭제 화면
- URL : /todo/modify
- method : GET
- 조회 화면에서 'Modify/Remove'를 클릭 했을 때 GET 방식으로 이동이 가능하도록 설계
- 수정/삭제 화면에서는 POST 방식으로 수정이나 삭제를 할 수 있는 버튼 추가
- /todo/list (GET) -> /todo/read (GET) -> /todo/modify (GET)

### 수정 (POST)
- URL : /todo/modify
- method : POST
- /todo/modify (GET) -> TodoModifyController doPost() -> /todo/list (Redirect) -> /todo/list (GET)

### 삭제 (POST)
- URL : /todo/modify
- method : POST
- /todo/modify (GET) -> TodoRemoveController doPost() -> Redirect

### 구현 목록 정리
|    기능    | 동작 방식 | 컨트롤러(org.zerock.w1.todo) |            JSP            |
|:--------:|:-----:|:------------------------:|:-------------------------:|
|   `목록`   |  GET  |    TodoListController    |   WEB-INF/todo/list.jsp   |
| `등록(입력)` |  GET  |  TodoRegisterController  | WEB-INF/todo/register.jsp |
| `등록(처리)` | POST  |  TodoRegisterController  |         Redirect          |
|   `조회`   |  GET  |    TodoReadController    |   WEB-INF/todo/read.jsp   |
| `수정(입력)` |  GET  |   TodoModifyController   |  WEB-INF/todo/modify.jsp  |
| `수정(처리)` | POST  |   TodoModifyController   |         Redirect          |
| `삭제(처리)` | POST  |   TodoRemoveController   |         Redirect          |

