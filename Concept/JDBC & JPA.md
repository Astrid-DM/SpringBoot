### JDBC
* JDBC(Java Database Connectivity)는 DB에 접근하 수 있도록 `java`에서 제공하는 `API`
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
* `findById` 하나 구현하는데도 코드가 이렇게나 길어진다. 이를 개선하기 위해 등장한것이 Spring JDBC Template이다.

### JDBC Template
``` java 
    @Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper(), id);
        return result.stream().findAny();
    }
```
* 위에 언급된 plain JDBC에 비해 코드가 훨씬 간결해졌다.
* Spring JDBC가 Connection의 열고 닫기, 예외 처리와 트랜젝션을 모두 처리해줘서 개발자는 datasource 설정, SQL 작성, 결과 처리 수행에만 집중 가능
* 코드가 간결해졌음에도 SQL Query문을 작성해야 하기 때문에 객체 지향 개발이 어렵다는 단점이 존재
  * 이를 개선하기 위해 JPA 등장
  
### JPA
```java
    private final EntityManager em;

    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
}
```
* JPA(Java Persistence API -> Jakarta Persistence API : 명칭 변경)
* Java의 ORM 표준
  * 객체와 DB 사이를 연결해주는 ORM(Object Relational Mapping)
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcVpdyx%2FbtrduVd3PbP%2FKR8xkT8seoKxrHKdiezGx1%2Fimg.png"
  </img>
  * 컴퓨터는 똑똑하지만 사람이 말할때 말하지 않아도 '눈치'를 통해 행동하는 것 만큼 똑똑하지 않다. 때문에 DB의 테이블에 있는 정보를 Java로 구현한 객체에 맵핑할 때, 자동으로 이루어지지 않고 ORM을 통해 이루어진다.
  * ORM이 없었다면 Select로 얻어낸 값들을 일일이 맵핑했어야 할 것이다.
  
