
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

