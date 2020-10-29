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


### CGI를 보완한 Servlet 
-  Web Application Server에 Web Container는 요청이 들어오면 Thread를 생성하고 Servlet을 실행시킨다.

### 그럼 Thread는 Servlet은 어떻게 실행시켜?
- Servlet은 크게 init, service, destory동작으로 나눈다.
- init: Servlet Instance를 생성
- service: 실제 기능이 수행되는 곳. Http Method에 따라서 doGet(), doPost(), doPut(), doDelete()로 처리
- destroy: 보통 container가 종료되는 시점에 호출, 특정 servlet이 로드/언로드 될때도 호출.

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

