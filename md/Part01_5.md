# 1.5 Model

웹 MVC 구조에서는 컨트롤러와 화면의 역할이 엄격하게 구분되어 있다. 컨트롤러에서는 화면에 필요한 데이터를
화면쪽으로 전달해주는데 이런 역할을 하는 객체를 `Model`이라고 한다.

## 모델과 3티어
- 웹 MVC입장에서 시스템 전체 구조로 보면 모델은 View 와 Controller을 제외한 남은 부분
- 모델을 담당하는 영역을 다시 세분화 시켜 `DB를 담당하는 영역`과 `비즈니스 로직을 처리하는 영역`으로 구분할 수 있음.
- 로직 처리를 `서비스 계층`, 데이터 처리를 `영속 계층`으로 분리한다.
    ```logcatfilter
    View - Controller - Model
      |         |          | 
     JSP     Servlet       |
                  Service-----Persistence                    
    ```
- 3티어 구조
  - 앞의 그림에서 MVC구조를 제외하고 전체 시스템의 구조를 정리하면 다음과 같은 3티어 구조가 됩니다.
  ```logcatfilter
  View Layer - Service Layer - Persistence Layer
       |              |               |
  JSP, Servlet      Logic            Data
  ```

## DTO (Data Transfer Object)
- 3티어와 같이 계층을 분리하는 경우, 계층 사이에서 데이터 교환이 일어난다.
- 이 경우 대부분은 한 개 이상의 데이터를 전달하기 때문에 여러 개의 데이터를 묶어서 하나의 객체로 전달하는 것을 `DTO`라고 한다.
- DTO는 특별한 규격이나 제약이 있는 것은 아니지만 대부분은 Java Beans 형태로 구성하는 경우가 많다.
- Java Beans는 다음과 같은 형식으로 구성 (최소한의 규칙 3가지)
    - 생성자가 없거나 반드시 파라미터가 없는 생성자 함수를 가지는 형태
    - 속성(멤버 변수)은 private으로 작성
    - getter/setter를 제공할 것
- 컨트롤러는 DTO를 구성해서 서비스 계층을 호출하기도 하고, 반대로 서비스 계층에서 DTO를 받기도 하기 때문에 서비스 계층 구성 전에 DTO를 위한 클래스를 먼저 구성한다.
- 그래서 다음과 같은 사항을 고려하여 `src.main.todo.dto`에 TodoDTO를 생성

## 서비스 객체
- DTO는 단순히 여러 데이터들을 묶어서 하나의 객체를 구성하는 용도로 사용하며 `서비스 객체 메소드들의 파라미터나 리턴 타입으로 사용된다.`
- 서비스 객체는 간단히 말하면 `Logic들의 묶음`이라고 할 수 있다.
- 서비스 객체는 프로그램이 구현해야 하는 기능들의 실제 처리를 담당
- 예를 들어 CRUD(create, read, update, delete) 기능들은 모두 서비스 객체에 모아서 구현된다.
- 예제에서는 컨트롤러들이 TodoService의 객체를 이용해 원하는 작업을 처리하도록 구성
    ```logcatfilter
    TodoListController          |---  TodoService
    TodoRegisterController      |---  TodoService
    TodoModifyController        |---  TodoService
    TodoRemoveController        |---  TodoService
    ```

## TodoService 클래스
```java
public enum TodoService {
    INSTANCE;

    public void register(TodoDTO todoDTO){
        System.out.println("DEBUG.............."+todoDTO);
    }
    
    public List<TodoDTO> getList(){
        
        List<TodoDTO> todoDTOS = IntStream.range(0,10).mapToObj(i -> {
            TodoDTO dto = new TodoDTO();
            dto.setTno((long)i);
            dto.setTitle("Todo.."+i);
            dto.setDueDate(LocalDate.now());
            
            return dto;
        }).collect(Collectors.toList());
        
        return todoDTOS;
    }
}
```
- `enum타입`으로 클래스를 작성하는 경우 가장 큰 장점은 정해진 수만큼만 객체를 생성할 수 있다는 점
- TodoService는 INSTANCE라고 된 부분이 객체의 개수를 결정하는 부분으로 하나의 객체만을 생성해서 사용하게 된다.
- 객체를 사용할 때는 `TodoService.INSTANCE`와 같이 간단하게 사용 가능
- 이처럼 객체를 하나만 생성하서 사용하는 패턴을 `Singleton Pattern`(싱글톤 패턴) 이라고 한다.
- TodoService's Methods
  - 원칙적으로 서비스 객체는 절대로 System.out.println()과 같이 출력하면 안 되는 존재지만, 디버깅을 위한 용도로는 사용할 수 도 있다.
  - register()의 경우 새로운 TodoDTO객체를 받아서 확인할 수 있는 것을 목표로 작성
  - getList()는 10개의 TodoDTO객체를 만들어서 반환하도록 구성

## 컨트롤러에서 모델 처리하기
- 웹 MVC 구조에서는 화면에 필요한 데이터를 처리하기 위해 컨트롤러는 서비스 객체의 힘을 빌려 처리한다.
- 예를 들어 화면에서 List 데이터가 필요할 경우 컨트롤러는 TodoService의 getList()의 결과를 받아서 JSP까지 전달하고 JSP에서는 이를 보여준다.
  1. TodoListController에서 TodoService의 getList()호출
  2. List<TodoDTO>객체를 /todo/list.jsp에 전달
  3. list.jsp에서는 List객체를 화면에 보여줌
- `이 과정에서 HttpServletRequest의 setAttribute()를 사용하여 키와 값의 형식으로 데이터를 보관한다.`

## JSP 파일에서 JSTL사용하기
- JSTL을 이용하기 위해서는 라이브러리가 존재해야만 하므로 build.gradle에 의존성 라이브러리 추가
  ```groovy
  dependencies {
    compileOnly('javax.servlet:javax.servlet-api:4.0.1')
  
    testImplementation("org.junit.jupiter:junit-jupiter-api:${junitVersion}")
    testRuntimeOnly("org.junit.jupiter:junit-jupiter-engine:${junitVersion}")
  
    implementation group: 'jstl', name: 'jstl', version: '1.2'
  }
  ```
- JSP에서 JSTL을 이용하기 위해서는 파일 상단에 다음과 같이 태그 관련 설정을 추가 해야한다.
  ```html
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
  ```
  > JSP에서 '<%@ %>'로 작성되는 코드를 '지시자(directives)'라고 한다.
- `<c:forEach>`
  - JSTL에서 가장 많이 사용하는 반복문 처리에 사용

  |    속성명    |                     속성값                      |
  |:---------:|:--------------------------------------------:|
  |    var    |                EL에서 사용될 변수 이름                |
  |   items   | List, Set, Map, Enumeration, Iterator 등의 컬렉션 |
  | begin/end |                  반복의 시작/끝 값                  |
  - list.jsp에 적용
  ```html
  <ul>
      <c:forEach var="dto" items="${list}">
          <li>${dto}</li>
      </c:forEach>
  </ul>
  ```
  - begin/end를 이용하는 경우
  ```html
  <ul>
      <c:forEach var="num" begin="1" end="10">
          <li>${num}</li>
      </c:forEach>
  </ul>
  ```
- `<c:if>`
  - test라는 속성이 존재하며 속성값으로는 true/false로 나올 수 있는 식이나 변수 등이 들어갈 수 있다.
  - <c:if>의 경우 else에 대한 처리가 없다.
  ```html
  <c:if test="${list.size() % 2 == 0}">
      짝수
  </c:if>
  <c:if test="${list.size() % 2 != 0}">
      홀수
  </c:if>
  ```
- `<c:choose>`
  - switch구문과 비슷한 역할
  - 내부에 <c:when test=..>, <c:otherwise>를 이용해서 'if ~else if ~ else'의 처리가 가능
  ```html
  <c:choose>
      <c:when test="${list.size() % 2 == 0}">
          짝수
      </c:when>
      <c:otherwise>
          홀수
      </c:otherwise>
  </c:choose>
  ```
- `<c:set>`
  - EL과 JSTL을 이용해서 반복문이나 제어문을 처리하다 보면 새로운 변수를 생성해야 하는 경우가 발생
  - 이러한 경우에 `<c:set>`을 이용하여 변수를 생성하고 사용할 수 있다.
  - var 속성으로 변수명을 지정할 수 있고, value 속성으로 값을 지정할 수 있다.
  ```html
  <c:set var="target" value="5"></c:set>
  
  <ul>
      <c:forEach var="num" begin="1" end="10">
          <c:if test="${num == target}">
              num is target
          </c:if>
      </c:forEach>
  </ul>
  ```


## Todo 조회
- 조회나 목록은 브라우저의 요청을 받아 컨트롤러에서 모델을 처리하는 좋은 예가 될 수 있다.
- 조회의 대부분의 경우 식별할 수 있는 값(식별키)를 GET방식으로 같이 요청한다.
- 예를 들어 상품의 번호나 회원의 아이디 같은 정보를 전달하고, 컨트롤러는 서비스 객체로 이를 반환해서 JSP로 전달
- TodoService에 특정 번호 조회 기능 추가
  ```java
  public TodoDTO get(Long tno){
        TodoDTO dto = new TodoDTO();
        dto.setTno(tno);
        dto.setTitle("Sample Todo");
        dto.setDueDate(LocalDate.now());
        dto.setFinished(true);
  
        return dto;
      }
  ```
- TodoReadController작성
  ```java
  @WebServlet(name = "todoReadController", urlPatterns = "/todo/read")
  public class TodoReadController extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      System.out.println("/todo/read");
  
      Long tno = Long.parseLong(req.getParameter("tno"));
  
      TodoDTO dto = TodoService.INSTANCE.get(tno);
  
      req.setAttribute("dto",dto);
  
      req.getRequestDispatcher("/WEB-INF/todo/read.jsp").forward(req,resp);
    }
  }
  ```
  - 추가된 get()메소드는 특정한 번호의 TodoDTO를 구하는 기능이지만 예제에서는 샘플용 TodoDTO객체를 생성하여 반환
  - getParameter()는 항상 문자열로만 결과가 나오기 때문에 Long타입 변환이 필요
  - TodoService의 get()을 통해 나온 TodoDTO객체는 'dto'라는 이름으로 JSP에 전달
- WEB-INF/todo폴더에 read.jsp작성
  ```html
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
    <title>Title</title>
  </head>
  <body>
    <div>${dto.tno}</div>
    <div>${dto.title}</div>
    <div>${dto.dueDate}</div>
    <div>${dto.finished}</div>
  </body>
  </html>
  ```
  > 프로젝트 실행후 '/todo/read?tno=123'과 같이 특정 번호를 파라미터로 전달하면 TodoDTO 객체의 내용을 볼 수 있다.


  