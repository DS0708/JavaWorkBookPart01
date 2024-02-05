# 1.1 웹 개발 환경 만들기

## 웹 프로젝트의 기본 구조
- 대부분의 웹 프로젝트들은 다음과 같이 여러 프로그램이 같은 네트워크를 통해서 연결되는 구조
- 브라우저 <-> 서버 <-> DB
- 브라우저 혹은 클라이언트 프로그램
  - 렌더링(rendering) : 서버에서 전송한 결과를 화면에 보여주는 것
  - 요청(request) : 사용자의 화면 조작을 이용해서 서버에 원하는 데이터를 보내는 것
  - 응답(response) : 그 요청에 대한 응답을 받는 것
  - 관련 기술 : HTML/CSS/JavaScript 등
- Web Server 혹은 Web Application Server(WAS)
  - Web Server : 이미지와 같은 고정된 데이터를 제공
  - WAS : 동적으로 매번 새로운 데이터를 만들어낼 수 있음
  - 실제 운영 환경에서는 보통 웹 서버와 WAS를 분리해서 운영하지만, 대부분의 WAS는 웹 서버 기능도 겸하고 있으므로 실습 시에는 WAS만으로 구성 가능
  - 관련 기술 : 서블릿/JSP, 각종 프레임워크와 언어들
- DB (DataBase)
  - 영구적으로 데이터를 보관하고 운영하기 위한 프로그램
  - 기본적으로 relational(관계형) 패러다임을 이용
  - 관련 기술 : SQL, DB 설계/구현

## 프로젝트 생성을 위한 세팅
- 이 책의 실습은 IntelliJ Ultimate버전으로 진행
- JDK 11 버전 사용 할 것
- WAS는 무료로 사용이 가능한 톰캣을 사용할 것이고 버전은 Tomcat9
  - [톰캣 사이트](https://tomcat.apache.org/download-90.cgi)에서 다운로드가능
  - Binary Distributions 중에 zip파일로 되어 있는 버전 선택
  - 나는 Core 에 zip파일을 다운로드하여 "/Users/ds/ds_study/Resource"에 저장


## 프로젝트 생성
- intelliJ의 new Project 선택
- Jakarta EE 항목을 선택하고 'Project template' 항목은 'Web application'으로 지정
- Application server에 방금 다운로드한 톰캣의 경로를 지정
- Project SDK 항목은 JDK11 을 선택, 없으면 Download JDK
- 개발하는 경우 여러 라이브러리가 필요하므로 설정을 편하게 할 수 있는 build 도구는 Gradle 선택
- Group : org.zerock 후 Next
- Version : Java EE 8
- 프로젝트 생성 마지막에는 Java Enter-prise와 관련된 여러 기술 라이브러리들을 설정할 수 있지만 기본적으로 'Servlet'만 지정할 것
- 생성 완료

## 프로젝트 경로 설정
- 프로젝트가 실행되면 브라우저의 주소창이 상당히 길고 복잡하므로 localhost:8080으로 간단하게 수정하기
- 실행중인 톰캣을 중지하고 
- Run -> Edit Configurations... -> Deployment 선택
- 기존에 있던 .war을 선택하고 [-] 버튼 클릭
- `war은 'Web Application Archive'의 약자로 현재 프로젝트를 압축 파일로 만들어서 톰캣에서 실행하는 방식`
- [+] 를 눌러 'Artifact'를 '(exploded)'가 포함된 항목으로 지정
- Application context를 '/'로 지정
- 톰캣을 다시 실행시 localhost:8080으로 브라우저의 경로가 지정되는지 확인

## 변경된 코드의 반영
- 그레이들을 이용해서 컴파일 등의 작업이 처리되고 프로젝트의 build/libs 폴더 안에 
내용은 톰캣을 통해서 실행하기 때문에 코드를 변경한 후에는 톰캣을 재시작해야만 함
- 톰캣의 재시작을 최소화하기 위해서
- Run -> Edit Config-urations... -> Server 탭
- 톰캣을 위한 한글 설정을 위해 VM options: '-Dfile.encoding=UTF-8'
- On 'Update' action 와 On frame deactivation을 `Update classes and resources`로 바꿔준다.
- 그 후 index.jsp를 변경하고 저장하고 브라우저를 새로고침 하면 변경이 적용됨.
- `index.jsp는 소스코드를 변경하는 것만으로 반영되지만 HelloServlet과 같은 자바 코드의 변경은 조금 다르게 처리된다.`
- 왼쪽 아래에 있는 'services' 항목에서 [Deploy All]을 실행하면 현재 코드를 다시 빌드하고 build/libs 폴더의 내용물을
수정하게 된다.
- 그럼 로그들이 출력되면서 서버 내에 변경된 코드가 반영된다.

## 서블릿 코드 작성
```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(name="myServlet",urlPatterns = "/my")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html");

        PrintWriter out = resp.getWriter();
        out.println("<html><body>");
        out.println("<h1>MyServlet</h1>");
        out.println("</body></html>");
    }
}
```
- 톰캣에서 작성하는 자바 코드는 HttpServlet이라는 클래스를 상속해서 작성한다.
- 이것을 흔히 서블릿 클래스를 생성한다고 표현
- @WebServlet 어노테이션은 브라우저의 경로와 해덩 서블릿을 연결하는 설정을 위해 사용
- doGet()은 브라우저의 주소를 직접 변경해서 접근하는 경우에 호출되는 메소드
- PrintWriter라는 객체를 이용해 브라우저쪽으로 출력을 처리


## JSP 코드 작성하기
- /src/main/webapp/에 'test.jsp' 생성
- "http://localhost:8080/test.jsp"를 통해 test.jsp접근 가능
