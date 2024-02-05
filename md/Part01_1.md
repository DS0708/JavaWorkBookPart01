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
