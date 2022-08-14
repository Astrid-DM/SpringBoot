## SOLID
* 코드의 재사용 및 유지보수가 용이하도록 지향하는 소프트웨어 설계 원칙
* 코드의 결합도(Coupling)은 낮추고 응집도(Cohesion)은 높이는것이 목표
* 아래 5가지를 합쳐서 SOLUD로 일컬음
  * SRP (Single Responsibility Principle)
  * OCP (Open Closed Principle)
  * LSP (Liskov Substitution Principle)
  * ISP (Interface Segregation Principle)
  * DIP (Dependency Inversion Principle)  

### 1. SRP (Single Responsibility Principle)
* 단일 책임의 원칙
  * 한 클래스는 하나의 책임만을 가진다.

[ 예시 1 ]
``` java
public class AppConfig {
      public MemberService memberService() {
          return new MemberServiceImpl(new MemoryMemberRepository());
}
      public OrderService orderService() {
          return new OrderServiceImpl(
                  new MemoryMemberRepository(),
                  new FixDiscountPolicy());
} }
```
* 위 코드에서 `AppConfig`는 구현 객체를 생성하고 연결하는 책임을 갖고있음
* 클라이언트 객체는 실행하는 책임만 담당


[ 예시 2 ]
``` c++
class Unit(){
    private String name;
    private int speed;
    
    public void move(){
    	if(name.equals("슬라임")){
        	speed+=3;
        }else if(name.equals("주황버섯"){
        	if(angry) speed = 10;
            	else speed = 5;
        }else{
        	// ... 
        }
    }
}
```
* 위의 예시에서는 몬스터가 추가되면 if-else가 끝없이 늘어날 수 있음
* 더 나아가 클래스 내부의 함수가 바뀔 경우, 또 다른 클래스에도 영향을 끼칠 수 있음  
``` c++
class 슬라임 extends Unit{
	public void move(){
    		this.speed += 3;
    }
}

class 주황버섯 extends Unit{
	public void move(){
          	if(angry) speed = 10;
          	else speed = 5;
    }
}
```
* 위 코드에 SRP를 적용하면 위와 같이 바뀜

### 2. OCP (Open Closed Principle)
* 자신의 확장에는 열려있으나, 주변의 변화에는 닫혀있음
``` java
public class AppConfig {
      public MemberService memberService() {
          return new MemberServiceImpl(new MemoryMemberRepository());
}
      public OrderService orderService() {
          return new OrderServiceImpl(
                  new MemoryMemberRepository(),
                  new FixDiscountPolicy()); // or RateDiscountPolicy
} }
```
* `AppConfig`에서 의존관계를 `FixDiscountPolicy` -> `RateDiscountPolicy`로 변경해서 클라이언트 코드에 주입하므로 클라이언트 코드는 변경하지 않아도 됨

### 3. LSP (Liskov Substitution Principle)
* 서브 타입은 언제나 자신의 기반(상위) 타입으로 교체가 가능해야함
<img width="60%" src="https://blog.kakaocdn.net/dn/bDNj5M/btrawB5k3M0/SYTlKGKzKt3KEp96fACkDK/img.png"/></img>
* 쉬운 예시로 모든 정사각형 클래스는 직사각형 클래스도 교체가 가능하나, 모든 직사각형 클래스가 모든 정사각형 클래스로 대체가 가능한 것은 아니다 

### 4. ISP (Interface Segregation Principle)
* 클라이언트 자신이 사용하지 않는 메서드에 의존관계를 맺으면 안된다.
``` c++
interface Animal {
  eat(): void;
  fly(): void;
}
```
``` c++
class Bird implements Animal {
  eat(): void {
    console.log('새가 음식을 먹었어요!');
  }
  fly(): void {
    console.log('새가 하늘을 날았어요!');
  }
}

class Human implements Animal {
  eat(): void {
    console.log('아 배부르다');
  }
  fly(): void {
    // ????
  }
}
```
* 사람은 하늘을 날 수 없다. 고로 fly() 함수는 사람 입장에서는 불필요한 메서드를 상솓받는 상황이 된다.
* 이를 위해 사람이 사람과 날으는 동물을 구분할 수 있는 인터페이스를 새로 만들면 된다.
``` c++
interface Animal {
  eat(): void;
}

interface FlyableAnimal extends Animal {
  fly(): void;
}

class Bird implements FlyableAnimal {
  eat(): void {
    console.log('새가 음식을 먹었어요!');
  }
  fly(): void {
    console.log('새가 하늘을 날았어요!');
  }
}

class Human implements Animal {
  eat(): void {
    console.log('아 배부르다');
  }
  // fly() 메서드를 구현하지 않는다. ISP 준수!
}
```
* 단, 상속 받는 클래스가 증가하면 SRP(단일책임원칙)에 위배될 수 있는데, 이는 프로젝트 요구사항에 따라 SRP와 ISP중 적절히 선택하면 된다.

### 5. DIP (Dependency Inversion Principle)
* 자신보다 변하기 쉬운 것에 의존하지 말아야한다.
    * 프로그래머는 추상화에 의존해야하지, 구체화에 의존하면 안된다.
``` java
   public class OrderServiceImpl implements OrderService {
      //private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
      private DiscountPolicy discountPolicy;
}
```
* 클라이언트 코드가 `DiscountPolicy` 추상화 인터페이스에만 의존하도록 코드를 변경함
* 주문서비스 클라이언트`OrderServiceImpl`는 `DiscountPolicy`인터페이스에 의존하면서 DIP를 지킴
    * 추상(인터페이스) 의존 : `DiscountPolicy`
    * 구체(구현) 클래스 : `FixDiscountPolicy`, `RateDiscountPolicy`
