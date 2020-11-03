# String WebFlux

### Spring Framework 주요기능?
- Dependency Injection / Inversion of Control
- Portable Service Abstraction
- Aspect Oriented Programming
 - 경량 컨테이너, POJO 지원, @Transaction 등등

### Spring Framework 기초
- 스프링 DI 기초와 의존 자동 주입
- 스프링 AOP 기초
- 스프링을 이용한 DB 연동
- 스프링 MVC 기본 동작 방식과 웹 어플리케이션 구조 이해
- 컨트롤러 구현, 요청, 폼, 세션/쿠키 처리
- JSON 요청/응답 처리
- 스프링 부트, 타임리프 템플릿 소개  

### Spring Framework 5.X 특징
- Java 호환 버전 변경: JDK8부터 지원
- Kotlin 지원
- 호환 라이브러리 변경 (JUnit 5, JDBC 4.0, Hibernate 5...)
- Reactive Programming 지원

### WebFlux 샘플 코드
```java
@Bean
public RouterFunction<?> routerFunction(){
    return route()
        .path(accept(APPLICATION_JSON), b2 -> b2
            .GET("", request -> ok()
                .body(repository.finalAll(), Crew.class))
            .POST("", request -> repository
                .saveAll(request.bodyToMono(Crew.class))
                .flatMap(crew -> created(URI.create("/crews" + crew.getId()))).build())
                .next())
            .GET("/{id}", request -> repository
                .findById(Long.valueOf(request.pathVariable("id")))
                .flatMap(crew -> ok().bodyValue(crew))
                .switchIfEmpty(notFound().build()))
            .DELETE("/{id}", request -> repository
                .findById(Long.valudOf(request.pathVariable("id")))
                .flatMap(repository::delete)
                .then(noContent().build()))
    )).build();
}
```

### Spring Webflux Documentation
[번역 - MadPlay님 블로](https://madplay.github.io/post/web-on-reactive-stack-spring-webflux)

> 테코톡 호돌님 강의 참고
