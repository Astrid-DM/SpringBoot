### Singleton을 적용하기 전
```java
    @Test
    @DisplayName("스프링 없는 순수한 DI 컨테이너")
    void pureContainer() {
        AppConfig appConfig = new AppConfig();
        //1. 조회: 호출할 때 마다 객체를 생성
        MemberService memberService1 = appConfig.memberService();
        //2. 조회: 호출할 때 마다 객체를 생성
        MemberService memberService2 = appConfig.memberService();
        //참조값이 다른 것을 확인
        System.out.println("memberService1 = " + memberService1);
        System.out.println("memberService2 = " + memberService2);
        //memberService1 != memberService2
        assertThat(memberService1).isNotSameAs(memberService2);
    }
```
* 테스트를 돌려보면 서로 다른 객체가 생성되었음을 확인 가능
``` shell
memberService1 = com.example.demo.member.MemberServiceImpl@4be29ed9
memberService2 = com.example.demo.member.MemberServiceImpl@4c60d6e9
```
* 초당 5만 TPS가 발생하는 서비스의 경우, 5만개의 객체가 생성되어야 하므로 굉장히 비효율적
* 스프링 없는 순수한 DI 컨테이너인 AppConfig는 요청을 할 때 마다 객체를 새로 생성
* 고객 트래픽이 초당 5만개가 발생하면 초당 5만개 객체가 생성되고 소멸
    * 메모리 낭비가 심하다. 해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. 
    * 이게 바로 싱글톤 패턴의 탄생 유래

### 싱글톤 패턴
* 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴
* 그래서 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 함
    * `private` 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 함 
```java
public class SingletonService {

    // 1. static 영역에 객체를 딱 1개만 생성
    private static final SingletonService instance = new SingletonService();

    // 2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용
    public static SingletonService getInstance() {
        return instance;
    }

    // 3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막음
    private SingletonService() {

    }

    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
}
```
* 아래와 같이 TC코드를 작성하여 동일한 객체가 호출되는지 확인
``` java
    @Test
    @DisplayName("싱글톤 패턴을 적용한 객체 사용")
    void singletonServiceTest(){
        SingletonService singletonService1 = SingletonService.getInstance();
        SingletonService singletonService2 = SingletonService.getInstance();

        System.out.println(singletonService1); // 1
        System.out.println(singletonService2); // 2
    }
```
``` shell
com.example.demo.singleton.SingletonService@5f049ea1
com.example.demo.singleton.SingletonService@5f049ea1
```
* 싱글톤 패턴을 적용하면 고객의 요청이 올 때 마다 객체를 생성하는게 아니라, 이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있다.


### 싱글톤 패턴의 단점
* 싱글톤 패턴을 구현하는 코드 자체가 많이 들어감
* 의존관계상 클라이언트가 구체 클래스에 의존
    * `DIP`=자신보다 변하기 쉬운것에 의존하지 말아야함을 위반 
* 클라이언트가 구체 클래스에 의존해서 `OCP(개방폐쇄)`을 위반할 가능성이 높음
* 테스트하기 어려움
* 내부 속성을 변경하거나 초기화 하기 어려움
* private 생성자로 자식 클래스를 만들기 어려움
* 결론적으로 유연성이 떨어짐
* 안티패턴으로 불리기도 함


### 싱글톤 컨테이너
* 스프링 컨테이너는 싱글턴 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리함
* 컨테이너는 객체를 하나만 생성해서 관리함
* 스프링 컨테이너는 싱글톤 컨테이너 역할을 하고, 이렇게 싱글톤 객체를 생성하고 관리하는 기능이 싱글톤 레지스트리라 불림
* 스프링 컨테이너의 이런 기능 덕분에 싱글턴 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있음
* 싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 됨
* DIP, OCP, 테스트, private 생성자로 부터 자유롭게 싱글톤을 사용할 수 있음

[싱글톤 컨테이너 예시]
``` java
 @Test
    @DisplayName("스프링 컨테이너와 싱글톤")
    void springContainer() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        //1. 조회: 호출할 때 마다 같은 객체를 반환
        MemberService memberService1 = ac.getBean("memberService", MemberService.class);
        MemberService memberService2 = ac.getBean("memberService", MemberService.class);
        // 참조값이 같은 것을 확인
        System.out.println("memberService1 = " + memberService1); System.out.println("memberService2 = " + memberService2);
        //memberService1 == memberService2
        assertThat(memberService1).isSameAs(memberService2);
    }
```
``` shell
memberService1 = com.example.demo.member.MemberServiceImpl@74cadd41
memberService2 = com.example.demo.member.MemberServiceImpl@74cadd41
```
* 스프링 컨테이너 덕분에 고객의 요청이 들어올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용 할 수 있음
* 참고로 스프링의 기본 빈 등록방식은 싱글톤이지만, 싱글톤 방식만 지원하는 것은 아니다. 요청할 때 마다 새로운 객체를 생성해서 반환하는 기능도 제공함 (빈 스코프 강의에서 다뤄짐)

### 싱글톤 방식의 주의점
* 여러 클라이언트가 하나의 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안됨
* 무상태(stateless)로 설계해야 됨
    * 특정 클라이언트에 의존적인 필드가 있으면 안됨
    * 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안됨
    * 가급적 읽기만 가능해야 함
    * 필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 함

```java
public class StatefulServiceTest {

    @Test
    void statefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean("statefulService", StatefulService.class);
        StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);

        //ThreadA: A사용자 10000원 주문
        statefulService1.order("userA", 10000);
        //ThreadB: B사용자 20000원 주문
        statefulService2.order("userB", 20000);
        //ThreadA: 사용자A 주문 금액 조회
        int price = statefulService1.getPrice();
        //ThreadA: 사용자A는 10000원을 기대했지만, 기대와 다르게 20000원 출력
        System.out.println("price = " + price);

        Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
    }

    static class TestConfig {
        @Bean
        public StatefulService statefulService() {
            return new StatefulService();
        }
    }
}
```
* 위 코드에서 실제 스레드는 사용되지 않음
* ThreadA가 사용자A 코드를 호출하고 ThreadB가 사용자B 코드를 호출한다 가정
* StatefulService 의 price 필드는 공유되는 필드인데, 특정 클라이언트가 값을 변경
* 사용자A의 주문금액은 10000원이 되어야 하는데, 20000원이라는 결과가 나옴
* 실무에서 이런 경우를 종종 보는데, 이로인해 정말 해결하기 어려운 큰 문제들이 터진다.(몇년에 한번씩 꼭 만난다.)
* 때문에 공유필드는 조심해야 한다! 스프링 빈은 항상 무상태(stateless)로 설계해야한다.

[변경 코드]
``` java
public class StatefulService {

    // private int price; // 상태를 유지하는 필드 // 10000 -> 20000으로 저장해버린 원흉

    public int order(String name, int price) { // 원래는 void였다
        System.out.println("name = " + name + " price = " + price);
        // this.price = price; //여기가 문제
        return price;
    }
}
```
``` java
    @Test
    void statefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean("statefulService", StatefulService.class);
        StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);

        //ThreadA: A사용자 10000원 주문
        int userA = statefulService1.order("userA", 10000);
        //ThreadB: B사용자 20000원 주문
        int userB = statefulService2.order("userB", 20000);

        //ThreadA: 20000원 정상 출력
        System.out.println("price = " + userA);
    }
```
``` shell
price = 10000 // 결과 정상 출력
```
* `stateful`한 상태를 `stateless` 상태로 변경
