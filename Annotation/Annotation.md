### @Transactional (in Test Code)
* DB 테스트를 실행할때 transaction을 싱행해주고, DB에 데이터를 넣은 다음 테스트 종료 후 롤백
  * 다시 말해 DB에 넣었던 데이터를 테스트 종료 후 제거
* @Transactional이 없을 경우, 통합 테스트(@SpringBootTest) 환경에서 테스슽 할 경우 실제로 DB에 데이터가 누적됨

### @ResponseBody
* Html의 본문(body)이 그대로 노출
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
