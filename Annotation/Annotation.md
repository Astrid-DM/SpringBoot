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
