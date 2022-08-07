### JDBC
* `JDBC(Java Database Connectivity)`는 DB에 접근하 수 있도록 `java`에서 제공하는 `API`
* 2000년대 초반에 많이 쓰임. 현재는 잘 안쓰이는데, 이유는 다음고 같다.
```java
    @Override
    public Optional<Member> findById(Long id) {
        String sql = "select * from member where id = ?";
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setLong(1, id);
            rs = pstmt.executeQuery();
            if(rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                return Optional.of(member);
            } else {
                return Optional.empty();
          }
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        } 
    }
```
* `findById` 하나 구현하는데도 코드가 이렇게나 길어진다. 이를 개선하기 위해 등장한것이 `Spring JDBC Template`이다.

### JDBC Template
``` java 
    @Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper(), id);
        return result.stream().findAny();
    }
```
* 위에 언급된 `plain JDBC`에 비해 코드가 훨씬 간결해졌다.
* `Spring JDBC`가 `Connection`의 열고 닫기, 예외 처리와 트랜젝션을 모두 처리해줘서 개발자는 datasource 설정, SQL 작성, 결과 처리 수행에만 집중 가능
* 코드가 간결해졌음에도 `SQL Query`문을 작성해야 하기 때문에 객체 지향 개발이 어렵다는 단점이 존재
  * 이를 개선하기 위해 `JPA` 등장
  
### JPA
```java
    private final EntityManager em;

    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
}
```
* `JPA`(Java Persistence API -> Jakarta Persistence API : 명칭 변경)
* Java의 ORM 표준
  * 객체와 DB 사이를 연결해주는 `ORM(Object Relational Mapping)`
  <img width="60%" src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcVpdyx%2FbtrduVd3PbP%2FKR8xkT8seoKxrHKdiezGx1%2Fimg.png"/></img>
  * 컴퓨터는 똑똑하지만 사람이 말할때 말하지 않아도 '눈치'를 통해 행동하는 것 만큼 똑똑하지 않다. 때문에 DB의 테이블에 있는 정보를 Java로 구현한 객체에 맵핑할 때, 자동으로 이루어지지 않고 ORM을 통해 이루어진다.
  * ORM이 없었다면 Select로 얻어낸 값들을 일일이 맵핑했어야 할 것이다.
* JPA는 Java의 ORM 표준으로 채택되어있다.
* ORM이 포괄적인 개념이라면 JPA는 구체적으로 기능을 정의한 스펙이라고 볼 수 있다.
* JPA의 `ORM Implementation`의 예시로 `Hibernate`가 있다. `Hibernate`는 `JPA Provider`의 표준(기본)이다.

### Spring DATA JPA
<img width="30%" src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FKNNL0%2Fbtrdr3Rbluo%2FYGi3SGnLwMZuWKnINIOHHk%2Fimg.png"/></img>
``` java
    // 인터페이스가 인터페이스를 상속받을때는 extends
    public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository {
        @Override
        Optional<Member> findByName(String name);
    }

```
* Spring에서 `Hibernate`를 보다 간편하게 사용할 수 있도록 추상객체를 한 번 더 감싸서 만든것
* EntityManager에 접근하지 않고도 보다 쉽게 객체에 접근하여 DB의 데이터를 활용 가능
* `findByName()`, `findByEmail()`처럼 메서드 이름 만으로 CRUD 기본 기능 제공
* 페이징 기능 자동 제공
* 실무에서는 보통 `JPA`와 `Spring Data JPA`를 사용하고, 복잡한 동적 쿼리는 `Querydsl`이라는 라이브러리를 사용
  * 이 조합으로도 해결하기 어려운 쿼리는 JPA가 제공하는 네이티브 쿼리, `JdbcTemplate`, 또는 `Mybatis`를 사용
  
### 정리
<img width="30%" src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FlfTql%2FbtrdwT1mWG5%2FNFTQeyb6ARWsTJuL7O9ecK%2Fimg.png"/></img>
* `JDBC(Java Database Connectivity)`는 `DB`에 접근할 수 있도록 `JAVA`에서 제공하는 `API`
* `ORM`은 어플리케이션 내부의 객체가 `DB`의 테이블에 쉽게 맵핑될 수 있도록 연결
* `JAVA`에서 `ORM`의 표준 스펙으로 `JPA`를 인터페이스로 정의하여 제공
    * 이때 해당 `JPA`의 실체 구현페이스를 모아둔 것이 `Hibernate`인데, 그 중 자주 사용되는 인터페이스들을 보다 쉽게 사용하기 위해 `Spring Framework`에서 다시 한번 묶음으로 제공한 것이 `Spring Data JPA`
