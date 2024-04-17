
- 간단하게 예전에는 이런 식으로 동작시켰어야 했었구나라고 이해만 하고 넘어가자.

#### 1. JDBC 환경설정

- `build.gradle`파일에 JDBC, H2 데이터베이스 관련 라이브러리 추가
```gradle
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
runtimeOnly 'com.h2database:h2'
```

- `resource/application.properties`에 스프링 부트 데이터베이스 연결설정 추가 
```null
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
```


#### 2. JDBC 리포지토리 구현

- 인텔리제이에서 코드 정렬 단축키는 (CTRL + SHIFT + ALT + L)이다. 
- 이를 알려주는 내용은 아래 내용을 붙여넣어야 하기 때문이다.

- `src/main/java/hello/hellospring/repository` 경로에 `MemberRepository` interface를 상송받는 또다른 리포지토리인 `JdbcMemberRepository.java`파일을 만들어주자.
	- 메모리에 member객체들을 저장하는 리포지토리는 `Memor`

```java
package hello.hellospring.repository;  
  
import hello.hellospring.domain.Member;  
import org.springframework.jdbc.datasource.DataSourceUtils;  
  
import javax.sql.DataSource;  
import java.sql.*;  
import java.util.ArrayList;  
import java.util.List;  
import java.util.Optional;  
  
public class JdbcMemberRepository implements MemberRepository {  
    private final DataSource dataSource;  
  
    public JdbcMemberRepository(DataSource dataSource) {  
        this.dataSource = dataSource;  
    }  
  
    @Override  
    public Member save(Member member) {  
        String sql = "insert into member(name) values(?)";  
        Connection conn = null;  
        PreparedStatement pstmt = null;  
        ResultSet rs = null;  
        try {  
            conn = getConnection();  
            pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);  
            pstmt.setString(1, member.getName());  
            pstmt.executeUpdate();  
            rs = pstmt.getGeneratedKeys();  
            if (rs.next()) {  
                member.setId(rs.getLong(1));  
            } else {  
                throw new SQLException("id 조회 실패");  
            }  
            return member;  
        } catch (Exception e) {  
            throw new IllegalStateException(e);  
        } finally {  
            close(conn, pstmt, rs);  
        }  
    }  
  
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
            if (rs.next()) {  
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
  
    @Override  
    public List<Member> findAll() {  
        String sql = "select * from member";  
        Connection conn = null;  
        PreparedStatement pstmt = null;  
        ResultSet rs = null;  
        try {  
            conn = getConnection();  
            pstmt = conn.prepareStatement(sql);  
            rs = pstmt.executeQuery();  
            List<Member> members = new ArrayList<>();  
            while (rs.next()) {  
                Member member = new Member();  
                member.setId(rs.getLong("id"));  
                member.setName(rs.getString("name"));  
                members.add(member);  
            }  
            return members;  
        } catch (Exception e) {  
            throw new IllegalStateException(e);  
        } finally {  
            close(conn, pstmt, rs);  
        }  
    }  
  
    @Override  
    public Optional<Member> findByName(String name) {  
        String sql = "select * from member where name = ?";  
        Connection conn = null;  
        PreparedStatement pstmt = null;  
        ResultSet rs = null;  
        try {  
            conn = getConnection();  
            pstmt = conn.prepareStatement(sql);  
            pstmt.setString(1, name);  
            rs = pstmt.executeQuery();  
            if (rs.next()) {  
                Member member = new Member();  
                member.setId(rs.getLong("id"));  
                member.setName(rs.getString("name"));  
                return Optional.of(member);  
            }  
            return Optional.empty();  
        } catch (Exception e) {  
            throw new IllegalStateException(e);  
        } finally {  
            close(conn, pstmt, rs);  
        }  
    }  
  
    private Connection getConnection() {  
        return DataSourceUtils.getConnection(dataSource);  
    }  
  
    private void close(Connection conn, PreparedStatement pstmt, ResultSet rs) {  
        try {  
            if (rs != null) {  
                rs.close();  
            }  
        } catch (SQLException e) {  
            e.printStackTrace();  
        }  
        try {  
            if (pstmt != null) {  
                pstmt.close();  
            }  
        } catch (SQLException e) {  
            e.printStackTrace();  
        }  
        try {  
            if (conn != null) {  
                close(conn);  
            }  
        } catch (SQLException e) {  
            e.printStackTrace();  
        }  
    }  
  
    private void close(Connection conn) throws SQLException {  
        DataSourceUtils.releaseConnection(conn, dataSource);  
    }  
}
```