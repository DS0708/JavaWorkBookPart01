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
