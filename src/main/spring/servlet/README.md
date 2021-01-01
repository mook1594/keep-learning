# Servlet

### 초기 웹의 모습
- Web Server (Apache)
- 주로 정적 데이터를 보여줌

### 조금 진화한 웹
- 동적 데이터를 보여주려는 니즈.
- CGI가 탄생

### CGI
- Web Server와 프로그램 사이의 규약

### 동적 데이터를 처리하는 CGI
- WebServer에 요청이 들어오면 process로 CGI 구현체를 실행시켜 데이터를 처리 후 response 돌려줌.
- 하지만 process의 많은 메모리 차지로 많은 사용자를 처리하기에 무리가됨.

### 그러면 많은 사용자를 처리하기 위해선?
- Process를 Thread로 변경하고
- Thread는 static 메모리를 공유하므로 CGI 구현체를 Singleton으로 한개의 instance로 처리
- Web Application Server

### Process vs Thread
- 두개의 차이는 [여기서 확인](../thread/README.md)


### CGI를 보완한 Servlet (자바로 구현된 CGI) 
-  Web Application Server에 Web Container는 요청이 들어오면 Thread를 생성하고 Servlet을 실행시킨다.

### Servlet container
- 서블릿과 웹서버가 쉽게 통신할 수 있게 해주어 웹소켓을 만들고 listen, accept 등 API로 제공하여 간단하게 구현

### 그럼 Thread는 Servlet은 어떻게 실행시켜? Servlet Life Cycle
- Servlet은 크게 init, service, destory동작으로 나눈다.
- init: Servlet Instance를 생성
- service: 실제 기능이 수행되는 곳. Http Method에 따라서 doGet(), doPost(), doPut(), doDelete()로 처리
- destroy: 보통 container가 종료되는 시점에 호출, 특정 servlet이 로드/언로드 될때도 호출. GC 실행

### 여기서 JVM 역할은?
- 각 요청들은 분리된 스레드 내부에서 처리
- JVM이 각 요청을 분리된 자바 스레드 내부에서 처리


### Spring Web MVC에서는 Servlet?
##### 요청이 들어오면 요청에 따라 적절한 Controller를 찾는다
- Dispatcher Servlet에서 Hanlder Mapping 에서 매핑된 핸들러를 찾는다

##### Handler Mapping은 어떻게?
- BeanNameHandlerMapping (빈 이름으로 등록 한다)
- ControllerClassNameHandlerMapping
- SimpleUrlHandlerMapping
- DefaultAnnotationHandlerMapping (annotation으로 매핑한다.)
- 핸들러 매핑은 web.xml, /WEB-INF/{servlet이름}-servlet.xml에 등록

##### HandlerMapping에서 찾은 Handler의 메서드를 호출하여 ModelAndView 형태로 변경한다.

##### View Resolver에서 View이름으로 View객체를 생성한다.

#### Spring Web Mvc 이전 vs 이후
- 이전: Url마다 Servlet을 생성하여 Web.xml로 servlet을 관리
- 이후: Servlet은 DispatcherServlet1개로 관리

### Apache
- WS 성능에 큰 영향을 주는건 메모리.
- 메모리 스왑은 요청당 지연시간을 늘리기에 WS는 스왑을 하면 안된다.
- Keep-Alive를 사용하면 자식들은 열린상태에서 추가요청을 계속 기다림. KeepAliveTimeout 기본값 15는 이런 현상 최소화
- Keep-Alive는 HTTPS 암호화 비용을 줄일 수 있다.
- [HTTPS 동작방식](../../network/https/README.md)

### Tomcat 인스턴스 구성
- 웹 애플리케이션은 `스케일-업` 보다는 `스케일-아웃` 에 더 적합한 구조

### maxThread 설정 기준 
- 톰캣과 같은 WAS는 설정해야하는게 많지만, 가장 성능에 많은 영향을 주는 부분은 maxThread 같은 Thread Pool에 직접적으로 연관된 설정
- Thread Pool에 대한 설정은 메모리를 얼마나 할당할 것인가 관련있고, Thread 수를 많이 사용할 수록 메모리를 많이 차지
- 적게 설정하면 서버에서 많은 요청을 처리하지 못하고 대기상태, 많이 설정하면 out.of.memory
- 어떤걸 고려해야할까? 웹 애플리케이션과 연관되는 시스템도 고려 
- 운영중인 서비스에서 DB 커넥션 값이 200에 가까운 수치가 설정되어있어 문제가 발생. (쓰레드가 많아져서 커넥션이 많이 유지됨)
- 그렇다고 maxThread < DB Connection Pool 은 효율적이지 못하다
- 왜? 애플리케이션에 대한 모든 요청이 DB에 접근하는 것은 아니기 때문에. WAS의 maxThread는 DB커넥션 수 보다 여유있게 설정하는 것이 좋다.

