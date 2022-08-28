### 먼저 알아두자
* 의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작함
* 스프링 빈으로 등록되지 않은 클래스에는 100번 1000번 `@Autowired` 코드를 적용해도 아무 기능도 동작 안함

### 의존 관계의 4가지 방법
* 생성자 주입
* 수정자 주입 (Setter)
* 필드 주입
* 일반 메서드 주입


### 1. 생성자 주입
* 생성자를 통해 의존 관계를 주입
* 생성자 호출 시점에 딱 1회만 호출되는 것이 보장됨
* **불편,필수** 의존관계에서 사용

```java
@Component
  public class OrderServiceImpl implements OrderService {
      private final MemberRepository memberRepository;
      private final DiscountPolicy discountPolicy;
      
      @Autowired
      public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy, discountPolicy) {
          this.memberRepository = memberRepository;
          this.discountPolicy = discountPolicy;
      }
}
```
* 만약 생성자가 딱 1개만 있으면 `@Autowired`를 생략해도 자동 주입됨

### 2. 수정자 주입 (Setter 주입)
* 필드의 값을 변경하는 setter 수정자 메서드를 통해 의존관계를 주입
* **선택, 변경** 가능성이 있는 의존관계에 사용
* 자바빈 프로퍼티 규약자 수정자 메서드 방식을 사용하는 방식

``` java
@Component
  public class OrderServiceImpl implements OrderService {
      private MemberRepository memberRepository;
      private DiscountPolicy discountPolicy;
      
      @Autowired
      public void setMemberRepository(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
      @Autowired
      public void setDiscountPolicy(DiscountPolicy discountPolicy) {
          this.discountPolicy = discountPolicy;
      }
}
```
* `@Autowired`는 주입할 대상이 없는 경우 오류가 발생함. 
  이를 방지하기 위해서는 `@Autowired(required=false)`를 적어주면 주입 대상이 없을 경우 해당 메서드 자체가 실행되지 않음 
### 3. 필드 주입
* 코드가 간결하다는 장점이 있지만 테스트가 까다롭다는 단점이 있음
* DI 프레임워크가 없으면 아무것도 할 수 없음
* 가급적 사용하지 말자
  * 어플리케이션의 실제 코드와는 관계성이 떨어지는 TC 코드를 작성하게 만들 가능성이 있음
  * 해당 방식은 스프링 설정을 목적으로 하는 `@Configuration`같은 곳에서만 특별한 용도로 사용
``` java
@Component
    public class OrderServiceImpl implements OrderService {
        @Autowired
        private MemberRepository memberRepository;
        @Autowired
        private DiscountPolicy discountPolicy;
}
```
* 순수 자바 테스트 코드에서는 당연히 `@Autowired`가 동작하지 않음
* `@SpringBootTest`처럼 스프링 컨테이너를 테스트에 통합한 경우에만 동작 가능

### 4. 일반 메서드 주입
* 한 번에 여러 필드를 주입받을 수 있음 
* 일반적으로는 잘 사용하지 않음
``` java
@Component
    public class OrderServiceImpl implements OrderService {
        private MemberRepository memberRepository;
        private DiscountPolicy discountPolicy;
        @Autowired
        public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
            this.memberRepository = memberRepository;
            this.discountPolicy = discountPolicy;
        }
}
```

### 자동 주입 대상을 옵션으로 처리하는 방법 (TC에서 예외처리 용도)
* `@Autowired` : 자동으로 주입할 대상이 없으면 수정자 메서드 자체가 호출 안됨
* `@Nullable` : 자동 주입할 대상이 없으면 `null`이 입력됨
* `Optional<>` : 자동 주입할 대상이 없으면 `Optional.empty`가 입력됨
``` java
     @Autowired(required = false)
    public void setNoBean1(Member member) {
        System.out.println("setNoBean1 = " + member);
        /* 이 경우엥는 Member가 스프링 빈으로 등족되어있지 아님 */
        /* 때문에 required = false가 아닌 경우 에러 발생 */
    }
    //null 호출
    @Autowired
    public void setNoBean2(@Nullable Member member) {
            System.out.println("setNoBean2 = " + member);
        }
    //Optional.empty 호출
    @Autowired(required = false)
    public void setNoBean3(Optional<Member> member) {
            System.out.println("setNoBean3 = " + member);
        }
```

### 베스트 옵션 : 1. 생성자 주입
* 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존관계를 변경할 일이 없음
* 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안됨
* 수정자 주입을 사용하면, setXxx 메서드를 public으로 열어두어야 함
* 생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없으므로, 불변하게 설계할 수 있음
    * 때문에 오직 생성자 주입 방식만 `final` 키워드 사용 가능
    * 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에서 막을 수 있음
``` java
@Component
  public class OrderServiceImpl implements OrderService {
      private final MemberRepository memberRepository;
      private final DiscountPolicy discountPolicy;
      
      @Autowired
      public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
          this.memberRepository = memberRepository;
      }
//...
}
```
* 프레임워크에 의존하지 않은 순수 자바의 특성을 가장 잘 살린 방법
