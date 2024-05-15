
#### 1. 스프링 JdbcTemplate란?

- **JdbcTemplate**은 Spring Framework에서 제공하는 JDBC 추상화 계층 중 하나이다.
	- 이는 JDBC API를 사용하여 데이터베이스와 상호 작용하는 과정을 단순화하고 개선하는 데 사용된다.

- DAY 17의 예제코드를 보면, **메소드마다** Connection을 생성하는 등의 동일한 구문을 포함하는 것을 볼 수  있을 것이다. **JdbcTemplate**을 사용하면 개발자는 JDBC의 반복적인 부분을 처리하는 코드를 줄일 수 있다.
	- 일반적으로 JDBC를 직접 사용하면 데이터베이스 연결을 열고 닫는 것부터 시작하여 SQL 쿼리 실행, 결과 집합 처리 등 다양한 부분을 직접 다루어야 한다.
	- 이러한 작업은 반복적이고 오류를 발생시킬 여지가 많다.
	- 그러나, SQL은 직접 작성해야 한다.

- 순수 JDBC와 동일한 환경설정을 하면 된다. 


#### 2. 스프링 JdbcTemplate 회원 리포지토리 생성하기 (1)

- 먼저, `src/main/java/hello/hellospring/repository` 하위에 `JdbcTemplateMemberRepository.java` 파일을 추가하고 아래처럼 작성해주자.
```java
package hello.hellospring.repository;  
  
import hello.hellospring.domain.Member;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.jdbc.core.JdbcTemplate;  
  
import javax.sql.DataSource;  
import java.util.List;  
import java.util.Optional;  
  
public class JdbcTemplateMemberRepository implements MemberRepository{  
  
    private final JdbcTemplate jdbcTemplate;  
  
    //  @Autowired : 생성자에 `@Autowired` 를 사용하면 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입 한다. 생성자가 1개만 있으면 `@Autowired` 는 생략할 수 있다  
    public JdbcTemplateMemberRepository(DataSource dataSource) {  
        this.jdbcTemplate = new JdbcTemplate(dataSource);  
    }  
  
    @Override  
    public Member save(Member member) {  
        return null;  
    }  
  
    @Override  
    public Optional<Member> findById(Long id) {  
        return Optional.empty();  
    }  
  
    @Override  
    public Optional<Member> findByName(String name) {  
        return Optional.empty();  
    }  
  
    @Override  
    public List<Member> findAll() {  
        return null;  
    }  
}
```

- DAY 17에도 생성자에 `DataSource`가 들어오는 부분이 있었다. 이에 대한 설명을 해보자면 아래와 같다.
> [!note] DataSource
> - `DataSource`는 데이터베이스 연결을 관리하는 객체이다. 보통은 데이터베이스 서버에 연결하고 쿼리를 실행하기 위한 정보를 포함하고 있다.
> - Spring 프레임워크에서는 데이터베이스 연동을 위해 DataSource 인터페이스를 사용한다. DataSource 인터페이스는 데이터베이스 연결을 설정하고 관리하는 메소드를 정의한다.
> - Spring에서 JdbcTemplate을 사용할 때는 JdbcTemplate 생성자에 DataSource를 전달하여 데이터베이스와의 연결을 설정한다. JdbcTemplate은 데이터베이스와의 연결을 설정하고 JDBC 작업을 수행하는 데 사용된다.
> - 따라서 `dataSource` 매개변수는, 여기선 JdbcTemplateMemberRepository 클래스의 생성자에 전달되는 DataSource 객체이다. 
> 	- 이를 사용하여 JdbcTemplate을 초기화하고 데이터베이스와의 연결을 설정할 수 있다.
> 	- 이후에는 JdbcTemplate을 사용하여 데이터베이스 쿼리를 실행하고 결과를 처리할 수 있다.

- 즉, `@Autowired`가 생략되었지만, 실질적으로는 스프링 컨테이너가 스프링 빈으로써 `DataSource`를 넣어주는 것이다.

- 일반적으로 Spring Boot 애플리케이션에서는 `application.properties` 또는 `application.yml` 파일에 데이터베이스 연결 정보가 설정된다.
	- Spring Boot는 이러한 설정 파일을 자동으로 읽어들여서 설정된 내용을 기반으로 DataSource 빈을 생성한다.
```js
spring.datasource.url=jdbc:h2:tcp://localhost/~/test  
spring.datasource.driver-class-name=org.h2.Driver  
spring.datasource.username=sa
```


#### 3. `findById` 구현하기

- 이전에는 어땠을까?
	- jdbcTemplate를 사용한 코드가 확실히 짧은 것을 체감할 수 있을 것이다.
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

```

- 이제 구현 결과를 확인해보자.
	- `jdbcTemplate.query` 메소드의 인자로, 원하는 쿼리와 함수를 전달하니, Member 타입의 List를 포함하는 `result`가 반환되는 것을 확인할 수 있다.
```java
@Override  
public Optional<Member> findById(Long id) {  
    List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper());  
    return result.stream().findAny();  
}

// ...

private RowMapper<Member> memberRowMapper(){  
    return (rs, rowNum) -> {  
        Member member = new Member();  
        member.setId(rs.getLong("id"));  
        member.setName(rs.getString("name"));  
        return member;  
    };  
}

```

- `jdbcTemplate.query(String sql, RowMapper<T> rowMapper, Object... args)` 메소드는 `jdbcTemplate`를 사용하여 SQL 쿼리를 실행한 것이다.
	- 첫 번째 매개변수인 `sql`은 실행할 SQL 쿼리문을 나타낸다.
	- 두 번째 매개변수인 `memberRowMapper()`는 결과 집합을 매핑할 RowMapper를 제공한다.
	- 세 번째 매개변수 `args`는 SQL 쿼리에 전달할 파라미터들을 나타낸다. 이 경우에는 `id`를 사용하여 SQL 쿼리에 바인딩된다.

-  `memberRowMapper()` 메서드는 `RowMapper<Member>`를 반환한다. 이는 결과 집합의 각 행(row)을 `Member` 객체로 매핑하는 데 사용된다. 
	- `RowMapper<Member>`는 `ResultSet`에서 각 행의 데이터를 추출하여 `Member` 객체로 변환하는 인터페이스이다.
	- `RowMapper<Member>`를 람다 표현식으로 구현한 부분에서는 `ResultSet`에서 회원의 ID와 이름을 추출하여 새로운 `Member` 객체를 생성하고 반환한다.
		- 이 때, 조회된 결과는 `List<Member>` 형태로 반환됩니다.
	- 정리하면, 쿼리 결과를 Mapper에서 받고, Mapper에서는 `resultSet`객체를 받아다가 member객체를 생성하고, 필요한 프로퍼티 세팅 후 member객체를 반환한다.

- `result.stream().findAny()`는 스트림(Stream)에서 임의의 요소를 반환하는 메소드이다.
	- `result`는 `List<Member>` 타입의 객체로, `stream()` 메소드를 호출하여 이 리스트를 스트림으로 변환한 후, `findAny()` 메서드를 호출하여 스트림에서 임의의 요소를 찾는다.
	- 만약, 리스트가 비어있다면 빈 `Optional`을 반환합니다.


#### 4. 나머지 코드 모두 작성

- 작성된 나머지 코드는 아래와 같다.
```js
package hello.hellospring.repository;  
  
import hello.hellospring.domain.Member;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.jdbc.core.JdbcTemplate;  
import org.springframework.jdbc.core.RowMapper;  
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;  
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;  
  
import javax.sql.DataSource;  
import java.sql.ResultSet;  
import java.sql.SQLException;  
import java.util.HashMap;  
import java.util.List;  
import java.util.Map;  
import java.util.Optional;  
  
public class JdbcTemplateMemberRepository implements MemberRepository {  
  
    private final JdbcTemplate jdbcTemplate;  
  
    //  @Autowired : 생성자에 `@Autowired` 를 사용하면 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입 한다. 생성자가 1개만 있으면 `@Autowired` 는 생략할 수 있다  
    @Autowired
    public JdbcTemplateMemberRepository(DataSource dataSource) {  
        this.jdbcTemplate = new JdbcTemplate(dataSource);  
    }  
  
    @Override  
    public Member save(Member member) {  
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);  
        jdbcInsert.withTableName("member").usingGeneratedKeyColumns("id");  
        Map<String, Object> parameters = new HashMap<>();  
        parameters.put("name", member.getName());  
        Number key = jdbcInsert.executeAndReturnKey(new MapSqlParameterSource(parameters));  
        member.setId(key.longValue());  
        return member;  
    }  
  
    @Override  
    public Optional<Member> findById(Long id) {  
        List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper());  
        return result.stream().findAny();  
    }  
  
    @Override  
    public List<Member> findAll() {  
        return jdbcTemplate.query("select * from member", memberRowMapper());  
    }  
  
    @Override  
    public Optional<Member> findByName(String name) {  
        List<Member> result = jdbcTemplate.query("select * from member where name = ?", memberRowMapper(), name);  
        return result.stream().findAny();  
    }  
  
  
    private RowMapper<Member> memberRowMapper() {  
        return (rs, rowNum) -> {  
            Member member = new Member();  
            member.setId(rs.getLong("id"));  
            member.setName(rs.getString("name"));  
            return member;  
        };  
    }  
}
```

- 다음으로, 테스트를 위해 `SpringConfig.java`파일에서 `memberRepository()` 메소드 내의 return값을 바꿔주자.

```js
package hello.hellospring;  
  
import hello.hellospring.repository.JdbcMemberRepository;  
import hello.hellospring.repository.JdbcTemplateMemberRepository;  
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
    // @Bean: 스프링 빈을 등록할 것이라는 annotation    @Bean  
    public MemberService memberService(){  

        return new MemberService(memberRepository());  
    }  
    @Bean  
    public MemberRepository memberRepository(){  
        // 구현체를 리턴해야함. 인터페이스 넣으면 안됨  
        // return new MemoryMemberRepository();  
        // return new JdbcMemberRepository(dataSource);        
        return new JdbcTemplateMemberRepository(dataSource);  
    }  
}
```

- 이제, `MemberServiceIntegrationTest.java`파일의 회원가입 테스트를 진행해보자.
	- 문제 없이 성공한 모습을 볼 수 있다.
![[JDBCTemplate 테스트.png]]