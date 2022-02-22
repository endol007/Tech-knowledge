## Static Pages & Dynamic Pages

#### Static Pages

- Web Server는 파일 경로 이름을 받아 경로와 일치하는 file contents를 반환한다.

- 항상 동일한 페이지를 반환한다.(HTML 페이지)

- html, css, javascript 와 같이 컴퓨터에 저장되어 있는 파일들

#### Dynamic Pages

- 인자의 내용에 맞게 동적인 contents를 반환한다.

- 즉, 웹 서버에 의해서 실행되는 프로그램을 통해서 만들어진 결과물

## Web Server

- Web Server
  - 소프트웨어와 하드웨어로 구분
  - 하드웨어
    - Web서버가 설치되어 있는 컴퓨터
  - 소프트웨어
    - 웹 브라우저 클라이언트로부터 http요청을 받아 정적인 컨텐츠(html, jpeg, css 등)를 제공하는 프로그램

- Web Server 기능
  - HTTP 프로토콜을 기반으로 하여 클라이언트(웹 브라우저 또는 웹 크롤러)의 요청을 서비스 하는 기능을 담당한다
  - 정적인 컨텐츠 제공 (WAS를 거치지 않고 바로 자원 제공)
  - 동적인 컨텐츠 제공을 위한 요청전달
    - 클라이언트의 요청을 WAS에 보내고 받은 응답을 클라이언트에 전달한다.
- Apache Server, Nginx, IIS 등이 있다.

## WAS(Web Application Server)

- WAS
  - DB조회나 다양한 로직 처리를 요구하는 동적인 컨텐츠를 제공하기 위해 만들어진 Application Server이다.
  - HTTP를 통해 컴퓨터나 장치에 애플리케이션을 수행해주는 미들웨어이다.
  - Web Container 혹은 Servlet Container라고도 부른다.
- Web Server와 Web Container 두개의 역할을 한다.
- Web Server의 기능들을 구조적으로 분리하여 처리하려고 만들어졌다.
  - DB서버와 같이 수행된다.
- 주요 기능
  - 프로그램 실행 및 DB접속 기능 제공
  - 여러개의 트랜잭션(논리적인 작업 단위) 관리 기능
  - 업무를 처리하는 비즈니스 로직 수행
- Tomcat, JBoss, Jeus, Web Sphere 등

## Web Server와 WAS

- Web Server가 필요한 이유
  - 클라이언트에서 요청을 보냈을 때, 이미지와 같은 정적인 컨텐츠들을 요청할 경우 Application Server까지 가지 않고 Web Server에서 가지고 있다가 앞단에서 빠르게 응답을 해줄 수 있다.
  - 그래서 Web Server를 가지고 기능을 분배하여 서버의 부담을 줄일 수 있다.
- WAS가 필요한 이유
  - 웹은 정적인 컨텐츠와 동적인 컨텐츠가 존재한다.
    - 클라이언트의 요청에 따라 적절한 동적인 컨텐츠를 만들어서 제공해야 한다.
    - Web Server만 이용할 경우는 모든 경우의 수에 대해 결과 값을 만들어 놓고 서비스를 해야하지만 이런 경우는 자원이 절대적으로 부족할 수 밖에 없다.
    - 그래서 WAS를 통해 요청에 맞는 데이터를 DB에서 가져와서 비즈니스 로직에 맞게 결과를 만들어서 제공하면서 자원을 효율적으로 활용할 수 있다.
- WAS와 Web Server를 같이 사용하는 이유
  - 기능 분리를 이용한 서버 부하 방지
    - WAS는 DB조회나 다양한 로직을 처리하기 바쁘기 때문에 정적인 컨텐츠들은 Web Server에서 빠르게 클라이언트에 제공하는 것이 좋다.
    - WAS는 기본적으로 동적인 컨텐츠를 제공하기 위해 존재하는 서버이다.
  - 보안 강화
    - SSL에 대한 암복호화 처리에 Web Server를 사용
  - 여러대의 WAS 연결 가능
    - Load Balancing을 위해서 Web Server를 사용
    - fail over, fail back 처리에 유리
  - 여러 웹 어플리케이션 서비스 가능
    - 하나의 서버에서 php와 java를 같이 사용하는 경우
  - 자원 이용의 효율성 및 장애 극복, 배포 및 유지보수의 편의성을 위해 분리함

## Web Service Architecture
- 다양한 구조를 가질 수 있다.
  - Client → Web Server → DB
  - Client → WAS → DB
  - Client → Web Server → WAS → DB

