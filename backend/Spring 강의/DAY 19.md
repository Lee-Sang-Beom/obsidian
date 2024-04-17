
#### 1. 스프링 JdbcTemplate란?

- **JdbcTemplate**은 Spring Framework에서 제공하는 JDBC 추상화 계층 중 하나이다.
	- 이는 JDBC API를 사용하여 데이터베이스와 상호 작용하는 과정을 단순화하고 개선하는 데 사용된다.

- JdbcTemplate을 사용하면 개발자는 JDBC의 반복적인 부분을 처리하는 코드를 줄일 수 있다.
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