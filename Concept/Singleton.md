// 이미지 추가해주기
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
