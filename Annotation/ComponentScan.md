### Component Scan
* `@Bean`이나 `xml`의 `<bean>`을 통해서 일일이 빈을 등록하지 않아도 자동으로 스프링 빈을 등록
* 컴포넌트 스캔은 이름 그대로 `@Component` 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록
``` java
   @Component
   public class MemberServiceImpl implements MemberService {
      private final MemberRepository memberRepository;
      
      @Autowired
      public MemberServiceImpl(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
}
}
```
* `@Autowired`는 의존 관계를 자동으로 주입해줌
![alt text](https://t1.daumcdn.net/cfile/tistory/21180341559E03A432)
* `@Component`, `@Controller`, `@Service`, `@Repository` 등의 어노테이션에도 전부 `@Component`가 포함되어있어 자동으로 컴포넌스 스캔의 대상이 됨


### 사용 예시

``` java
@Configuration
@ComponentScan(
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```
* 여기서 excludeFilter는 해당 타겟은 스캔 목록애서 제외한다는 뜻
* 반대로 includeFilter는 특정 타켓을 스캔 목록에 포함한다는 뜻

``` java
public class AutoAppConfigTest {

    @Test
    void basicScan() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);

        MemberService memberService = ac.getBean(MemberService.class);
        assertThat(memberService).isInstanceOf(MemberService.class);
    }
}
```
* 해당 테스트의 로그를 보면 컴포넌트 스캔이 잘 동작됨을 확인할 수 있음
``` java
20:25:59.943 [Test worker] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'autoAppConfig'
20:25:59.946 [Test worker] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'rateDiscountPolicy'
20:25:59.947 [Test worker] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memberServiceImpl'
20:25:59.956 [Test worker] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'memoryMemberRepository'
```


### 수동 빈 vs 자동 빈
* `@Bean`을 통해 수동으로 빈을 등록하고, 동시에 `@Component`를 통해 자동으로 빈을 등록할 경우
  우선 권은 `@Bean`이 가짐
* 수동 빈이 자동 빈을 오버라이딩

### 컴포넌트 스캔 기본 대상 + 관련 어노테이션
* `@Controller` : 스프링 MVC 컨트롤러로 인식
* `@Repository` : 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환
* `@Configuration` : 스프링 설정 정보로 인식하고 스프링 빈이 싱글톤을 유지하도록 추가 처리
* `@Service` : 사실 특별한 역할은 없음. 하지만 이 어노테이션을 적어주면 핵심 비즈니스 로직이 여기 있겠구나 정도로 비즈니스 계층을 인식하는데 도움이 됨
