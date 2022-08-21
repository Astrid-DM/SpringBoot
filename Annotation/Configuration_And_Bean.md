### 스프링으로 전환하기
* `@Configuration` 적용
    * [코드]  
    ``` java
    @Configuration // 기존 코드에는 없었던 어노테이션
    public class AppConfig {
    ```
* `@bean` 적용
    * [코드]
    ``` java
    @Configuration
    public class AppConfig {

    @Bean // 기존 코드에는 없었던 어노테이션
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemoryMemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
        
    ... (생략)
}
    ```
### 사용 코드 예시
``` java
public class MemberApp {

    public static void main(String[] args) {
        // AppConfig appConfig = new AppConfig(); // 기존 코드
        // MemberService memberService = appConfig.memberService(); // 기존 코드

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);

        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("findMember = " + findMember.getName());
    }
}
```
* `ApplicationContext`(스프링 컨테이너)에서 `AppConfig.class`를 주입
* `@Configuration`이 붙은 `AppConfig`를 설정 정보로 사용
* 여기서 `@Bean`이라 적힌 메서드를 모두 호출해 반환된 객체를 스프링 컨테이너에 등록 = 스프링 빈
* 이렇게 등록된 스프링 빈들은 스프링 컨테이너에서 관리되고, 스프링 빈은 `application.getBean()`을 통해 찾을 수 있다.
* 기존에는 개발자가 직접 자바코드로 모든 메서드를 관리했다면, 이제는 스프링 컨테이너에서 스프링 빈을 찾도록 변경되었다.

### AppConfig.class의 장점
* SRP 단일 책임 원칙을 만족시킨다.
    * 한 클래스는 하나의 책임만을 가진다는 단일 책임 원칙이다. 구현 객체를 생성하고 연결하는 책임은 `AppConfig`가 담당한다.
      클라이언트 객체는 실행하는 책임만 담당한다.
* OCP 개방 폐쇄 원칙을 만족시킨다.
    * 클라이언트 코드를 수정하지 않고 `AppConfig` 클래스의 코드를 수정하여 의존관계를 변경할 수 있다.
* DIP 의존관계 역전 원칙을 만족시킨다.
    * 클라이언트코드가 추상화 인터페이스에만 의존할 수 있도록 코드를 변경하였다.

### 스프링의 장점
* `AppConfig`를 활용한 java 코드만으로도 SRP, OCP, DIP를 만족할 수 있지만, 스프링은 이에 더해 싱글톤 패턴 등
  다양한 기능들이 있다.