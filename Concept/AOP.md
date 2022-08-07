### AOP
* `AOP(Aspect Oriented Programming)` 관점 지향 프로그래밍
* 여러 객체에 공통으로 적용할 있는 기능을 구분함으로써 재사용을 높여주는 프로그래밍 기법
  * 예시) 메소드 호출 시간 측정
* 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리


## 실행 예시 - 메소드 호출 시간 측정

## 실행 결과
``` shell
2022-08-07 20:22:36.173  INFO 3548 --- [           main] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
2022-08-07 20:22:36.177  INFO 3548 --- [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
START: execution(MemberService com.example.hellospring.SpringConfig.memberService())
END: execution(MemberService com.example.hellospring.SpringConfig.memberService()) 6ms
2022-08-07 20:22:36.447  WARN 3548 --- [           main] JpaBaseConfiguration$JpaWebConfiguration : spring.jpa.open-in-view is enabled by default. Therefore, database queries may be performed during view rendering. Explicitly configure spring.jpa.open-in-view to disable this warning
2022-08-07 20:22:36.556  INFO 3548 --- [           main] o.s.b.a.w.s.WelcomePageHandlerMapping    : Adding welcome page: class path resource [static/index.html]
```
