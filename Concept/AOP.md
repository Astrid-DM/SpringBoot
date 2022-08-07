### AOP
* `AOP(Aspect Oriented Programming)` 관점 지향 프로그래밍
* 여러 객체에 공통으로 적용할 있는 기능을 구분함으로써 재사용을 높여주는 프로그래밍 기법
  * 예시 1) 메소드 호출 시간 측정
  * 예시 2) JSON 규격 통신 맞추기 (ex : A사는 UTF-8, B사는 UTF-16)
* 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리

## AOP 관련 주요 Annotation
* @Aspect	자바에서 널리 사용하는 AOP 프레임워크에 포함되며, AOP를 정의하는 Class에 할당
* @Pointcut	기능을 어디에 적용시킬지, 다시말해 메소드, Annotion 등 AOP를 적용시킬 지점을 설정
* @Before	메소드를 실행하기 이전
* @After	메소드가 성공적으로 실행 된 후, 예외가 발생되더라도 실행
* @AfterReturning	메소드 호출 성공 실행 시 (Not Throws)
* @AfterThrowing	메소드 호출 실패 예외 발생 (Throws)
* @Around	Before / After 모두 제어

## 실행 예시 - 메소드 호출 시간 측정
``` java
// TimeTraceAop.java
@Component // Bean으로 등록
@Aspect // AOP 선언
public class TimeTraceAop {

    @Around("execution(* com.example.hellospring..*(..))") // 현재 실행중인 패키지의 명을 정확히 입력
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("START: " + joinPoint.toString());
        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("END: " + joinPoint.toString()+ " " + timeMs +"ms");
        }
    }
}

```

## 실행 결과
``` java
2022-08-07 20:22:36.173  INFO 3548 --- [           main] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
2022-08-07 20:22:36.177  INFO 3548 --- [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
START: execution(MemberService com.example.hellospring.SpringConfig.memberService())
END: execution(MemberService com.example.hellospring.SpringConfig.memberService()) 6ms
2022-08-07 20:22:36.447  WARN 3548 --- [           main] JpaBaseConfiguration$JpaWebConfiguration : spring.jpa.open-in-view is enabled by default. Therefore, database queries may be performed during view rendering. Explicitly configure spring.jpa.open-in-view to disable this warning
2022-08-07 20:22:36.556  INFO 3548 --- [           main] o.s.b.a.w.s.WelcomePageHandlerMapping    : Adding welcome page: class path resource [static/index.html]
```

## 실행 원리
* 프로그램이 실행되고 스프링 컨텍스트에 스프링 빈이 올라갈 때, 프록시(가짜 스프링 빈)가 올라간다.
* 이때 진짜 스프링 빈이 실행될떄, 프록시 내부에서 `joinPoint.proceed()`가 호출되어 AOP가 실행됨
