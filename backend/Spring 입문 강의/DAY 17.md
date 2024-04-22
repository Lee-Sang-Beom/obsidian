
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
	- 메모리에 member객체들을 저장하는 리포지토리는 `MemoryMemberRepository.java`
	- DB에 member객체들을 저장하는 리포지토리는 지금 생성하는 파일이라고 이해하면 된다.

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
	    //
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
	        // 리소스반환
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

- `insert`문 중 아래와 같은 쿼리가 보일 것이다.
```java
String sql = "insert into member(name) values(?)";  
```

- 위에서 `?`는 는 SQL 쿼리에서 사용되는 placeholder이다.
	- 이것은 Prepared Statement 라이브러리를 사용할 때 유용하게 쓰인다.
	- 이 기술을 사용하면 쿼리의 일부로 직접 값을 삽입하는 것이 아니라, 쿼리와는 별도로 값을 전달할 수 있다.
	- 이렇게 하면 보안 측면에서 더 안전하며, 쿼리 최적화 및 재사용성을 높일 수 있다.
		- 대개 `PreparedStatement` 클래스를 사용하여 이러한 작업을 수행합니다.

```java

// 1번 설명
String sql = "insert into member(name) values(?)";  
Connection conn = null;  
PreparedStatement pstmt = null;  
ResultSet rs = null;  
try {  
	// DB와의 connection 정보를 가져온다.
    conn = getConnection();  
    
	// 2번 설명
    pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);  

	// 3번 설명
    pstmt.setString(1, member.getName());  

	// DB에 실제 쿼리 날아감
    pstmt.executeUpdate();
    
    // Statement.RETURN_GENERATED_KEYS를 사용함으로써, 자동으로 생성한 키를 반환받을 수 있음
    // 4번 설명 
    rs = pstmt.getGeneratedKeys();

	// rs.next()는 데이터베이스 결과 집합에서 다음 행으로 이동하고 해당 행이 있는지 여부를 확인하는 메서드  
	if (rs.next()) {
	// `getLong(1)`은 ResultSet의 현재 행에서 지정된 인덱스(1부터 시작)에 해당하는 데이터를 long 형식으로 가져오는 메서드이다.  
    member.setId(rs.getLong(1));  
	} else {  
	    throw new SQLException("id 조회 실패");  
	}
} catch(Exception e){
// ...
}
// ...
```

1. 위의 코드에서 `?`는 `PreparedStatement`에 전달되는 값을 나타내며, `PreparedStatement` 인터페이스의 `setString()` 메서드를 사용하여 이 값을 설정한다.
	- 이렇게 하면 사용자 입력 등으로부터의 **SQL 삽입 공격(SQL injection)** 을 방지할 수 있다.
	- 사용자 입력에 직접 값을 삽입하는 대신 `PreparedStatement`를 사용하면, 입력 값이 이미 쿼리에 삽입되기 전에 적절하게 escape되어 쿼리의 일부로 해석되지 않는다.

2. SQL 쿼리를 실행할 `preparedstatement` 객체를 생성한다.	
	- 이 때, `Statement.RETURN_GENERATED_KEYS` 옵션을 설정하면, 데이터베이스가 **자동으로 생성한 키**를 반환하여 사용자가 이를 이용할 수 있게 한다.
	- `pstmt.getGeneratedKeys()`메소드를 사용하면 된다.

3. `pstmt.setString(1, member.getName());`에서의 `1`은 `placeholder`의 인덱스를 나타낸다.
	- 이는 SQL 쿼리에서 `?`로 표시된 각 `placeholder`의 위치를 지정하는 것이다.
	- 여기서는, `insert into member(name) values(member.getName())`이 될 것이다.

4. `ResultSet` 객체는 데이터베이스 쿼리를 실행한 결과를 가리키는 커서 역할을 하며, 이를 통해 데이터베이스로부터 가져온 행(row)들을 하나씩 탐색하고 해당 행의 데이터를 가져올 수 있다.
	- 만약, 아래와 같은 테이블이 있다고 가정해보자. 
	- 맨 처음 행에 대하여 `resultSet`에서 데이터를 가져오는 부분이 아래와 같이 구현되어 있다면 아래 처럼 할당이 이루어질 것이다.
		- `id = 1`, `name='user1'`
![[id and name table.png]]

###### ※ 참고 - **Optional**  
 * Optional은 Java 8에서 소개된 클래스로, 값이 있을 수도 있고 없을 수도 있는 값을 나타내는 컨테이너 클래스이다.  
 
 * 이 클래스는 **NullPointerException**을 방지하고 코드의 명확성을 높이는 데 사용된다.  
	 *  Optional은 값이 있을 때 그 값을 포함하고, 값이 없을 때는 빈 상태를 나타낸다.  
	 * 이를 통해 메서드가 항상 `null`을 반환할 가능성이 있는 경우를 다룰 때 특히 유용하다.  
```java
Optional<String> maybeName = getName();

if (maybeName.isPresent()) {
    String name = maybeName.get();
    System.out.println("Name is: " + name);
} else {
    System.out.println("Name is not available.");
}
```


#### 3. 스프링 설정 변경

```java
package hello.hellospring;  
  
import hello.hellospring.repository.JdbcMemberRepository;  
import hello.hellospring.repository.MemberRepository;  
import hello.hellospring.repository.MemoryMemberRepository;  
import hello.hellospring.service.MemberService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
import javax.sql.DataSource;  
  
 
@Configuration  
public class SpringConfig {  
  
    private DataSource dataSource;  
  
    @Autowired  
    public SpringConfig(DataSource dataSource){  
        this.dataSource = dataSource;  
    }  
    
    @Bean
    public MemberService memberService(){  
        return new MemberService(memberRepository());  
    }  
    @Bean  
    public MemberRepository memberRepository(){  
        return new JdbcMemberRepository(dataSource);  
  
    }  
}
```

- `SpringConfig`는 `application.properties`에서 설정된 내용을 기반으로 스프링이 자체적으로 Bean을 생성해준다.
	- 데이터베이스와 연결할 수 있는 DataSource를 만들어준다.
	- DI를 위해, 위와 같이 구성해주면 된다.

- 정리하면, 아래와 같다.
	- DataSource는 데이터베이스 Connection 획득할 때 사용하는 객체이다.
	- 스프링 부트는 데이터베이스 Connection 정보를 바탕으로 DataSource를 생성하고, 스프링 빈으로 만들어둔다. 
	- 그래서 DI를 받을 수 있다.

- 이제 프로젝트를 실행하여, 회원가입을 진행해보면 아래와 같이 정상적으로 잘 동작한 결과가 나온다.
	- user5, user6을 추가했다
![[프로젝트 실행결과 1.png]]
![[프로젝트 실행결과 2.png]]


#### 4. 참고

- 현재 DB Connection 이후의 설정 상황은 아래와 같다.

> 구현 클래스 추가 이미지 
- 데이터를 관리하는 리포지토리가 메모리를 사용하는 리포지토리와 JDBC 를 사용하는 리포지토리가 있다.
![[구현 클래스 추가 이미지.png]]

> 스프링 설정 이미지 (스프링 빈에서, memberService는 JdbcMemberRepository 인스턴스 사용)

![[스프링 설정 이미지.png]]

```java
package hello.hellospring;  
  
import hello.hellospring.repository.JdbcMemberRepository;  
import hello.hellospring.repository.MemberRepository;  
import hello.hellospring.repository.MemoryMemberRepository;  
import hello.hellospring.service.MemberService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
import javax.sql.DataSource;  
  

@Configuration  
public class SpringConfig {  
  
    private DataSource dataSource;  
  
    @Autowired  
    public SpringConfig(DataSource dataSource){  
        this.dataSource = dataSource;  
    }  
    @Bean  
    public MemberService memberService(){  
	    // memberRepository()는 JdbcMemberRepository를 반환
        return new MemberService(memberRepository());  
    }  
    @Bean  
    public MemberRepository memberRepository(){  
        return new JdbcMemberRepository(dataSource);  
  
    }  
}
```

- MemoryRepository를 사용하는 것에서 JdbcMemberRepository 사용하기 위해, 우리가 바꿔준 건 2가지 뿐이다.
	- JdbcMemberRepository 구현
	- 스프링 빈 파일 수정 (memberService가 JdbcMemberRepository를 사용할 수 있게 초기 세팅)
	- 이 부분에서, **OCP**라는 것을 지켰다고 볼 수 있다.

- 개방-폐쇄 원칙(OCP, Open-Closed Principle)
	- 확장에는 열려있고, 수정, 변경에는 닫혀있다는 뜻
	- 기능을 확장하되, 기존 로직을 하나도 손대지 않았음. (단지 확장을 위한 코드 추가와 세팅 설정을 했을 뿐이다.)

- 스프링의 DI (Dependencies Injection)을 사용하면 **기존 코드를 전혀 손대지 않고, 설정만으로 구현 클래스를 변경**할 수 있다.

- 데이터를 DB에 저장하므로 스프링 서버를 다시 실행해도 데이터가 안전하게 저장된다.