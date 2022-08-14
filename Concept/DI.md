### DI 개념
* Dependency Injection, 즉, 의존성 주입
    * 외부에서 객체를 주입 받음
* 클라이언트의 코드 변경 없이도 기능 확장이 가능한 것이 DI의 목표
* SRP(Single Responsibility Principle)과 밀접한 연관
* IoC(제어의 역전, Inversion of Control)과 비슷한 의미를 가지고 있음
* IoC 컨테이너, DI 컨테이너라고도 불림
 
### 함께 봐두면 좋을 키워드
* Spring Container : 개발자가 작성한 코드를 스스로 참조한 뒤 알아서 객체의 생성과 소멸을 컨트롤 해줌
* Bean : 스프링 컨테이너에서 관리되는 객체
* DI : 외부에서 객체를 주입받음
* IoC : 스프링 컨테이너가 객체를 관리함

### DI 예시
```java
   public class OrderServiceImpl implements OrderService {
      private DiscountPolicy discountPolicy;
}
```
* `OrderServiceImpl`은 `DiscountPolicy`인터페이스에 의존하며, 실제 어떤 구현 객체가 사용될지는 모른다.
* `FixDiscountPolicy`, `RateDiscountPolicy` 둘중 무엇을 주입받을지는 외부에서 결정이 된다.
* 이렇게 프로그램의 제어 흐름을 직접 제어하지 않고 외부에서 관리하는 것을 제어의 역전(IoC)이라고 한다.
