<img width="30%" src="https://3513843782-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-LxjHkZu4T9MzJ5fEMNe%2Fsync%2F21012b333f698d2d366ad35304db7e559cd641d9.png?generation=1618052456312074&alt=media"/></img>
### BeanFactory
* 스프링 컨테이너의 최상위 인터페이스
* 스프링 빈을 관리하고 조회하는 역할을 담당
* getBean() 을 제공한다.
* 지금까지 우리가 사용했던 대부분의 기능은 BeanFactory가 제공하는 기능

### ApplicationContext
* BeanFactory 기능을 모두 상속받아서 제공
* BeanFactory의 기능 외에 수많은 부가기능을 제공

<img width="60%" src="https://3513843782-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-LxjHkZu4T9MzJ5fEMNe%2Fsync%2Fad9b87e9ee7590f5f82a2bc0e554d33c87cd91b5.png?generation=1618052455370924&alt=media"/></img>
* 메시지소스를 활용한 국제화 기능
    * 예를 들어서 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력 
* 환경변수
    * 로컬, 개발, 운영등을 구분해서 처리 
* 애플리케이션 이벤트
    * 이벤트를 발행하고 구독하는 모델을 편리하게 지원
* 편리한 리소스 조회
    * 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

### 결론
* ApplicationContext는 BeanFactory의 기능을 상속받는다.
* ApplicationContext는 빈 관리기능 + 편리한 부가 기능을 제공한다.
* BeanFactory를 직접 사용할 일은 거의 없다. 부가기능이 포함된 ApplicationContext를 사용한다. 
* BeanFactory나 ApplicationContext를 스프링 컨테이너라 한다.
