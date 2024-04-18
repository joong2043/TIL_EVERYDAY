# JDBC ? Spring JDBC ?

개발자에게 로우레벨 기술, 하이레벨 기술 중 어떤게 더 중요할까?

로우레벨 : 저수준의 기술
하이레벨 : 고수준의 기술

### JDBC ?
- RDBMS에 접근하여 SQL문을 실행하기 위한 자바 라이브러리 (API)
- 모든 자바의 데이터 액세스 기술의 근간
- 엔티티클래스와 어노테이션을 이용하는 최신 ORM 기술
- 안정적이고 유연한 기술이지만, 로우 레벨 기술 

### Spring JDBC?
- JDBC의 장점과 단순성을 그대로 유지하면서 기존 JDBC의 단점을 극복한다.
- 단순한, 간결한 형태의 API 사용법을 제공
- JDBC를 개발자들이 사용하기 쉽게 만든 라이브러리입니다.
- 반복해야 하는 작업을 대신 처리해줍니다.

### Spring JDBC 역할
- Connection 열기 닫기
- statement 실행 -> sql 던지고 다양한 객체 받기 가능
- Exception 처리와 반환
- Transaction 처리
  - ex. 커밋, 롤백

### JdbcTemplate
- SpringJDBC의 모든 기능을 최대한 활용할 수 있는 유연성 제공
- 실행 
  - INSERT, UPDATE
- 조회
  - SELECT
- 배치
  - 여러개의 쿼리를 한꺼번에 수행
  - *필자는 배치 INSERT를 할 때 JdbcTemplate을 사용해본 경험이 있다.*

### JdbcTemplate을 안 쓰면?
JdbcTemplate을 안 쓰고 Connection으로 DB에 접근하면 ?
~~~java
	@Override
    public Member save(Member member) {
        String sql = "INSERT INTO MEMBER(member_id, money) VALUES (?, ?)";

        Connection con = null;
        PreparedStatement pstmt = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, member.getMemberId());
            pstmt.setInt(2, member.getMoney());
            pstmt.executeUpdate();
            return member;
        } catch (SQLException e) {
            throw exceptionTranslator.translate("save", sql, e);
        } finally {
            // TCP/IP 커넥션으로 연결되기 때문에 자원 해제해줘야 함
            close(con, pstmt, null); // 현재 코드에는 없지만 자원을 해제해주는 사용자 정의 메서드
        }
    }
~~~
커넥션 생성, 커넥션 닫기, statement 선언, statement 실행, 예외처리 등등 수동으로 처리해야할 부분이 너무 많음!!ㅠ 

### JdbcTemplate을 사용하면?
~~~java
private final JdbcTemplate template;		
        public MemberRepository(DataSource dataSource) {
        	this.template = new JdbcTemplate(dataSource);
    	}
		...
 
		@Override
   		public Member save(Member member) {
            String sql = "INSERT INTO MEMBER(member_id, money) VALUES (?, ?)";
            template.update(sql, member.getMemberId(), member.getMoney());
        return member;
    }
~~~

sql문 선언하고 간단한 메소드로 쿼리문 실행이 가능! 😃 

BUT, JDBC의 경우 dataSource를 제공받아야 하기 때문에 ! 
jdbcTemplate이 필요한 DataSource 빈을 의존 주입받아서 jdbcTemplate 생성할 때 인자로 넘겨준다!

### JdbcTemplate 단점
JdbcTemplate의 경우 sql문을 선언하고 실행하는 거여서 동적쿼리를 사용해야 할 때 조금 불편할 수 있다.
하지만, 동적쿼리도 sql문에 파라미터 형태로 인자를 받으면 가능하기는하다.
이게 불편하면, JPA + QueryDSL을 적용하는 것도 방법! 

