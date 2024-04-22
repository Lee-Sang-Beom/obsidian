
#### 1. Spring Data JPA (스프링 데이터 JPA)

- **스프링 데이터 JPA**는 스프링 프레임워크와 **JPA**를 함께 사용하여 데이터 액세스를 간소화하는 데 도움을 주는 프레임워크이다.
	- JPA는 자바 표준 ORM(Object-Relational Mapping) 기술로, 객체와 관계형 데이터베이스 간의 매핑을 처리한다.
	- 스프링 데이터 JPA는 이러한 JPA를 기반으로 하여, 데이터베이스 Access(접근)를 보다 쉽고 간편하게 만들어준다.

- 스프링 데이터 JPA를 사용하면 **CRUD(Create, Read, Update, Delete)** 작업과 쿼리 메소드의 작성이 간편해지며, 반복적이고 일반적인 데이터 액세스 작업을 자동화할 수 있다. 
	- 또한 스프링 데이터 JPA는 리포지토리 인터페이스를 정의하여, 애플리케이션에서 데이터베이스와의 상호 작용을 추상화하고 단순화할 수 있도록 지원한다.


#### 2. 스프링 데이터 JPA 회원 리포지토리

```java
package hello.hellospring.repository;  
  
import hello.hellospring.domain.Member;  
import org.springframework.data.jpa.repository.JpaRepository;  
  
import java.util.Optional;  
  
// 인터페이스가 인터페이스를 받을 때는, extends  
public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository {  
    Optional<Member> findByName(String name);  
}
```

- 먼저, 이번에는 `src/main/java/hello/hellospring/repository` 하위에, 인터페이스 자바 파일을 만들어, 위의 코드를 작성해주자.
	1. `public interface` 
		- 이것은 자바에서 인터페이스를 정의하는 키워드이다.
		- 인터페이스는 클래스와 달리 구현을 가지지 않으며, 메소드와 상수만을 정의한다.
	    
	2. `SpringDataJpaMemberRepository`
		- 이것은 인터페이스의 이름으로,  개발자가 선택한 것이며, 보통 해당 인터페이스가 하는 일을 나타내는 이름을 선택합니다.
	    
	3. `extends JpaRepository<Member, Long>`
		- 이 부분은 Spring Data JPA의 핵심이다.
		- JpaRepository는 스프링 데이터 JPA가 제공하는 인터페이스로, 데이터 액세스 작업을 위한 기본적인 CRUD 메소드를 제공한다.
		- 이 때, `Member`는 Entity 클래스를 나타내며, `Long`은 해당 Entity의 기본 키의 데이터 형식을 의미한다.
	    
	4. `, MemberRepository`
		- 이 부분은 추가적인 사용자 정의 메소드를 포함하는 인터페이스를 지정한다.
		- JpaRepository에서 제공되지 않는 특별한 동작이 필요한 경우 해당 인터페이스를 추가할 수 있다.

- 이렇게 하면, 스프링 데이터 JPA가 JpaRepository를 받고 있는 인터페이스에 대한 구현체를 자동으로 만들어준다.
	- 이렇게 하면, 스프링 빈을 자동으로 등록해준다.
	- **정리하면, JpaRepository를 `extends`하는 인터페이스가 있다면, 스프링 데이터 JPA가 그에 관련된 구현체를 스스로 만들어서 스프링 빈을 등록해준다.**


#### 3. 스프링 데이터 JPA 회원 리포지토리를 사용하도록, 스프링 설정 변경

- 다음으로, `SpringConfig` 파일에서 스프링 데이터 JPA가 만들어준 스프링 빈을 가져다 쓸 수 있도록 해야 한다.
	- 그 코드는 아래와 같다.

```java
package hello.hellospring;  
  
import hello.hellospring.repository.*;  
import hello.hellospring.service.MemberService;  
import jakarta.persistence.EntityManager;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
import javax.sql.DataSource;  
  
// 스프링이 실행될 때, Configuration을 먼저 읽고, config 내부의 @Bean annotation으로 설정된 요소를 스프링이 스프링 컨테이너에 스프링 빈으로 등록한다.  
@Configuration  
public class SpringConfig {  
  
    private final MemberRepository memberRepository;  

	// 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입해준다.
    @Autowired  
    public SpringConfig(MemberRepository memberRepository){  
	    // 여기에서, 스프링 데이터 jpa가 구현체를 만든 것이 이리로 들어옴
        this.memberRepository = memberRepository;  
    }  
  
    @Bean  
    public MemberService memberService(){  
	    // 의존관계 세팅: MemberService > memberRepository
        return new MemberService(memberRepository);  
    }  
  
}
```