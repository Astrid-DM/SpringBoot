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

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemoryMemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        return new RateDiscountPolicy();
    }
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
* 스프링 컨테이너 내부에서 생성된 `Bean` 객체는 모두 `Singleton` 패턴으로 관리되어진다.

### AppConfig에서 의문점
* memberService 빈을 만드는 코드를 보면 memberRepository() 를 호출 
   * 이 메서드를 호출하면 new MemoryMemberRepository() 를 호출 
* orderService 빈을 만드는 코드도 동일하게 memberRepository() 를 호출
   * 이 메서드를 호출하면 new MemoryMemberRepository() 를 호출
* 결과적으로 각각 다른 2개의 MemoryMemberRepository 가 생성되면서 싱글톤이 깨지는 것 처럼 보임
* 하지만 실제로 싱글톤 패턴이 깨지지 않은다.
   * `CGLIB`라는 바이트 코드 조작 라이브러리를 사용해서 `AppConfig`클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록함
   * 이때 임의의 다른 클래스가 바로 싱글톤이 되도록 보장함
   * `@Bean`이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진다.
   * 덕분에 싱글톤은 보장됨
