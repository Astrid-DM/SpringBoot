### @Transactional (in Test Code)
* DB 테스트를 실행할때 transaction을 싱행해주고, DB에 데이터를 넣은 다음 테스트 종료 후 롤백
  * 다시 말해 DB에 넣었던 데이터를 테스트 종료 후 제거
* @Transactional이 없을 경우, 통합 테스트(@SpringBootTest) 환경에서 테스트 할 경우 실제로 DB에 데이터가 누적됨
#
### @ResponseBody
* Html의 본문(body)이 그대로 노출
  * 다시 말해 View가 아닌 Text가 전달됨
* 기본적인 Default는 Json으로 데이터를 만들어서 HTTP 응답에 반환하도록 설정
  * 아래 경우엔 hello 객체가 Json으로 응답
  ``` java
  @GetMapping("hello-api")
  @ResponseBody
  public Hello helloApi(@RequestParam("name") String name){
      Hello hello = new Hello();
      hello.setName(name);
      return hello;
  }

  static class Hello {
      private String name;

      public String getName() {
          return name;
      }

      public void setName(String name) {
          this.name = name;
      }
  }
  ```
  ``` json
  {
    name: "spring!!!"
  }
  ```

### localDate
- 날짜 정보만 필요할 때 사용
``` java
LocalDate currentDate = LocalDate.now();
// result : 2019-11-13
```

### localTime
- 시간 정보가 필요할 때 사용
``` java
LocalTime currentTime = LocalTime.now();
// result : 18:34:22
```

### localDateTime
- 날짜와 시간 정보 모두 필요할 때 사용
``` java
LocalDateTime currentDateTime = LocalDateTime.now();
// result : 2019-11-12T16:34:30.388
```

### @NotNull
- `Null`만 허용하지 않음
- "", " "(공백 띄어쓰기)는 허용
- `CharSequence`에만 사용 가능 (ex : Enum, 특정 클래스 사용 가능)
- `Collection`, `Map`, `Array`에도 적용 가능하나, `Empty`를 허용함

### @NotEmpty
- `null`과 "" 둘다 허용
- " "(공백 띄어쓰기)는 허용
- `CharSequence`에만 사용 가능 (ex : Enum, 특정 클래스 사용 가능)
- `Collection`, `Map`, `Array`에도 적용 가능하나, `size` 또는 `length`가 0보다 커야함

### @NotBlank
- `null`, "", " "(공백 띄어쓰기) 모두 허용하지 않음
- `String`에만 사용 가능
- 문자열의 크기가 0보다 커야함

### @Valid
``` java
@GetMapping("/api")
  public ResponseEntity getTestAPi(@Valid TestApiDto.Request request,) {
```
```java
public class TestApiDto {

    @Getter
    @Builder
    @NoArgsConstructor(access = AccessLevel.PRIVATE)
    @AllArgsConstructor
    public static class Request {
        @Nullable
        List<String> testNames;
        @NotEmpty
        List<UseType> testTypes;
    }
}
```
- `JSR-303` 자바 표준 스펙
- `@Valid`를 붙여주면 해당 컨트롤러의 메소드에서 유효성 검증이 진행됨
- `Controller` 에서만 동작
- 다른 계층에서 파라미터를 검증하기 위해서는 `@Validated`와 결합해야함

### @Validated
- 자바 표준 스펙이 아닌 스프링 프레임워크가 제공하는 기능
- `@Validated`는 클래스에서 유효성 검증이 진행됨
- `@Validated`는 AOP 기반으로 메소드 요청을 인터셉터해서 처리
- 사용시 해당 컨트롤러, 서비스, 레포지토리 등 계층에 무관하게 스프링 빈이라면 유효성 검증 진행 가능

### @Relation
- `EntityModel`이나 `CollectionModel`의 `HAL`내부의 Embedded Object의 관계를 설정할때 사용
``` java
@Relation(collectionRelation = "history")
public class RewardUseHistoryDto {
    private String userId;
}
```
- 위와 같이 사용했을때 `application/hal+json`으로 요청시, 리턴되는 객체의 프로퍼티는
  `_embedded.RewardUseHIstoryDto.userId`가 아닌 `_embedded.history.userId`이다.

### @SuppressWarnings("unchecked")
- `@SuppressWarnings`는 컴파일경고를 사용하지 않도록 설정해줌
- 여기서 `unchecked`는 미확인 오퍼레이션과 관련된 경고를 억제함

### @Captor
- `MockitoExtension`을 쓸때만 동작
- 위 어노테이션 없이 같은 기능을 동작시키려면 아래와 같이 선언
``` java

ArgumentCaptor<Xxx> argumentCaptor = ArgumentCaptor.forClass(Xxx.class);
```
- `UnnecessaryStubbingExceptions`를 피하고 싶다면 `@ExtendWith(MockitoExtension.class)`대신 `@MockitoSettings(strictness.LENIENT)`를 선언하고 `@captor`/`@Mock`을 사용


### HATEOAS
- *Hypermedia As The Engine Of Application State*의 줄임말
- 링크에 사용 가능한 URL을 리소스로 전달하여 client가 참고하여 사용할 수 있도록 함 (아래 *_links* 참고)
```java
{
    "customerId": "10A",
    "customerName": "Jane",
    "_links":{
         "self":{
            "href":"http://localhost:8080/spring-security-rest/api/customers/10A"
            "rel" : "customers"
            "type" : "GET"
            }
        }
    }
```
- URL을 정의하는 대표적인 형식으로는 아래 2가지가 존재
     - *RFC 5988* (web linking)
     - *__JSON Hypermedia API Language(HAL)__*
- URL을 정의하는 속성으로는 아래 3가지가 존재
     - `href` : 타겟 URL (해당 API를 호출하는데 사용되는 URL)
     - `rel` : 링크 관계 (해당 API를 호출하여 궁극적으로 얻게되는 데이터)
     - `type` : 리소스 미디어 타입 (ex : GET, POST, PUT, DELETE)

### HAL (HyperText Application Language)
* API 리소스 사이에 하이퍼링크를 쉽게 제공하기 위한 방법
* JSON 또는 XML 형식으로 하이퍼링크를 표현하는 컨벤션 제공

__[General Resource]__
``` json
{
  "_links": {
    "self": {
      "href": "http://example.com/api/book/hal-cookbook"
    }
  },
  "id": "hal-cookbook",
  "name": "HAL Cookbook"
}
```
__[Embedded Resource]__
``` json
{
  "_links": {
    "self": {
       "href": "http://example.com/api/book/hal-cookbook"
    }
  },
  "_embedded": {
    "author": {
      "_links": {
        "self": {
          "href": "http://example.com/api/author/shahadat"
       }
      },
      "id": "shahadat",
      "name": "Shahadat Hossain Khan",
      "homepage": "http://author-example.com"
    }
  },
  "id": "hal-cookbook",
  "name": "HAL Cookbook"
}
```
__[Collections]__
``` java
{
  "_links": {
    "self": {
      "href": "http://example.com/api/book/hal-cookbook"
    },
    "next": {
      "href": "http://example.com/api/book/hal-case-study"
    },
    "prev": {
      "href": "http://example.com/api/book/json-and-beyond"
    },
    "first": {
      "href": "http://example.com/api/book/catalog"
    },
    "last": {
      "href": "http://example.com/api/book/upcoming-books"
    }
  },
  "_embedded": {
    "author": {
      "_links": {
        "self": {
          "href": "http://example.com/api/author/shahadat"
        }
      },
      "id": "shahadat",
      "name": "Shahadat Hossain Khan",
      "homepage": "http://author-example.com"
    }
  },
  "id": "hal-cookbook",
  "name": "HAL Cookbook"
}
```
* 보통 ***Resources***와 ***Links***의 컨셉을 가짐
    * Resources
        * Links (to URLs)
        * Embedded Resources
        * State (정제되지 않은 JSON 또는 XML 데이터)
    * Links
        * href (a URL)
        * rel (링크의 관계)
        * type (리소스 미디어 타입 ex :GET, PUT)
* HAL은 `application/hal+json`, `application/hal+xml`이라는 이름의 JSON, XML 미디어 타입을 가짐
* HTTP에 요청할 때, 응답 `Content-Type`은 관련된 미디어 타입을 포함해야함
* 최소한 `JSON` 객체와 `_links.self` URL은 가져야 함

### HATEOAS <-> HAL
* `HATEOAS`는 ***Application Architecture***의 한 개념
    * ***hypemedia*** 링크를 제공함으로써 클라이언트가 서버와 소통할 수 있게 제공함
* `HAL`은 `HATEOAS`를 어떻게 제공할 것인가에 대한 방법 중 하나
