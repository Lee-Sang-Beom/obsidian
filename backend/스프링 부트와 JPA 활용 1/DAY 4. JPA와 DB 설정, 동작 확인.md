
#### 1. `application.yml` 생성

```yml
spring:  
  datasource:  
    url: jdbc:h2:tcp://localhost/~/jpashop  
    username: sa  
    password:  
    driver-class-name: org.h2.Driver  
  jpa:  
    hibernate:  
      ddl-auto: create  
    properties:  
      hibernate:  
        #show_sql: true  
        format_sql: true  
  
logging:  
  level:  
    org.hibernate.SQL: debug
```

#### 2. 테스트 환경 추가

> 회원 엔티티 추가 (`src/main/java/jpabook/jpashop/Member.java`)
```java
package jpabook.jpashop;  
  
import jakarta.persistence.Entity;  
import jakarta.persistence.GeneratedValue;  
import jakarta.persistence.Id;  
import lombok.Getter;  
import lombok.Setter;  
  
@Entity  
@Getter @Setter  
public class Member {  
    @Id  
    @GeneratedValue    private Long id;  
    private String username;  
}
```

> 회원 리포지토리 추가 (`/src/main/java/jpabook/jpashop/MemberRepository.java`)
```java
package jpabook.jpashop;  
  
import jakarta.persistence.EntityManager;  
import jakarta.persistence.PersistenceContext;  
import org.springframework.stereotype.Repository;  
  
@Repository  
public class MemberRepository {  
  
    // @PersistenceContext annotatation은 엔티티 매니저(EntityManager)를 주입받는 데 사용된다.  
    // 스프링 프레임워크에서는 이 annotatation을 사용하여 엔티티 매니저를 주입받아서 JPA를 사용할 수 있도록 한다.  
  
    // 따라서 주어진 코드에서 @PersistenceContext annotatation이 붙은 `private EntityManager em;`은 엔티티 매니저를 주입받는 필드를 선언하는 것이다.  
    // 이를 통해 JPA를 사용하여 데이터베이스와 상호 작용할 수 있게 된다.  
    @PersistenceContext  
    private EntityManager em;  
  
  
    public Long save(Member member){  
        em.persist(member);  
        return member.getId();  
    }  
  
    public Member find(Long id){  
        // Member.class는 JPA의 EntityManager을 사용하여 데이터베이스에서 엔티티를 검색하는 메소드이다.  
        // 즉, Member.class는 Member 엔티티 클래스의 Class 객체를 나타낸다.  
        // 즉, 데이터베이스에서 주어진 클래스(Member.class)의 엔티티를 검색하기 위해 Member.class라는 걸 1번째 파라미터 인자로 넘기는 것이고, 그를 통해 해당 기본 키(id) 값을 기반으로 엔티티를 식별하여 반환할 수 있는 것이다.  
        return em.find(Member.class, id);  
    }  
}
```

> 테스트 코드 작성 (`/src/test/java/jpabook/jpashop/MemberRepositoryTest.java`)
```java
package jpabook.jpashop;  
  
import org.junit.jupiter.api.Assertions;  
import org.junit.jupiter.api.Test;  
import org.junit.runner.RunWith;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.test.context.junit4.SpringRunner;  
  
import static org.assertj.core.api.Assertions.assertThat;  
import static org.junit.jupiter.api.Assertions.*;  
  
/**  
 * @RunWith  
 * 이 annotation은 JUnit 테스트를 실행할 때 스프링의 테스트 컨텍스트 프레임워크를 사용하도록 지정합니다.  
 * SpringRunner 클래스는 JUnit 테스트를 실행할 때 스프링의 테스트 지원을 활성화하는 역할을 합니다  
 */  
  
/**  
 * @SpringBootTest  
 * 이 annotation은 스프링 부트 애플리케이션의 테스트를 위해 사용됩니다.  
 * 이 애노테이션을 사용하면 테스트를 실행할 때 스프링 부트 애플리케이션의 전체 컨텍스트를 로드하게 됩니다.  
 *  즉, 스프링 부트 애플리케이션과 같은 환경에서 테스트를 수행할 수 있게 됩니다.  
 */ 

/**  
 * @Autowired  
 * 이 annotation은 스프링의 의존성 주입(Dependency Injection)을 수행합니다.  
 * MemberRepository 타입의 빈을 자동으로 주입하도록 지정합니다.  
 * 이 경우 MemberRepository는 스프링 컨텍스트에서 관리되는 빈이므로, 해당 빈을 자동으로 주입하여 테스트에서 사용할 수 있게 됩니다.  
 */
@RunWith(SpringRunner.class)  
@SpringBootTest  
public class MemberRepositoryTest {  
    @Autowired MemberRepository memberRepository;  
  
    @Test  
    public void testMember() throws Exception {  
        // given (준비): 테스트를 위해 준비하는 과정으로, 테스트에 사용하는 변수, 입력 값 등을 정의  
        Member member = new Member();  
        member.setUsername("memberA");  
  
        // when (실행) : 실제로 액션을 하는 테스트를 실행  
        Long saveId = memberRepository.save(member);  
        Member findMember = memberRepository.find(saveId);  
  
        // then (검증) : 테스트를 검증하는 과정으로 예상한 값, 실제 실행을 통해 나온 값의 비교  
        assertThat(findMember.getId()).isEqualTo(member.getId());  
        assertThat(findMember.getUsername()).isEqualTo(member.getUsername());  
  
    }  
}
```


#### 3. 에러 해결

- 위의 테스트 코드를 실행하면, 아래와 같은 에러가 발생할 것이다.
	- 왜냐하면, Entity Manager를 통한 **모든 데이터 변경은 항상 트랜잭션 안에서 이루어져야 하기 때문**이다.
![[DAY3 테스트 에러.png]]

- 일단은 아래 코드처럼, `testMember()` 메소드 자체에 `@Transactional` annotation을 걸어주자.
```java
package jpabook.jpashop;  
  
import org.junit.jupiter.api.Assertions;  
import org.junit.jupiter.api.Test;  
import org.junit.runner.RunWith;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.test.context.junit4.SpringRunner;  
import org.springframework.transaction.annotation.Transactional;  
  
import static org.assertj.core.api.Assertions.assertThat;  
import static org.junit.jupiter.api.Assertions.*;  
  
/**  
 * @RunWith  
 * 이 annotation은 JUnit 테스트를 실행할 때 스프링의 테스트 컨텍스트 프레임워크를 사용하도록 지정합니다.  
 * SpringRunner 클래스는 JUnit 테스트를 실행할 때 스프링의 테스트 지원을 활성화하는 역할을 합니다  
 */  
  
/**  
 * @SpringBootTest  
 * 이 annotation은 스프링 부트 애플리케이션의 테스트를 위해 사용됩니다.  
 * 이 애노테이션을 사용하면 테스트를 실행할 때 스프링 부트 애플리케이션의 전체 컨텍스트를 로드하게 됩니다.  
 *  즉, 스프링 부트 애플리케이션과 같은 환경에서 테스트를 수행할 수 있게 됩니다.  
 */  
 
/**  
 * @Autowired  
 * 이 annotation은 스프링의 의존성 주입(Dependency Injection)을 수행합니다.  
 * MemberRepository 타입의 빈을 자동으로 주입하도록 지정합니다.  
 * 이 경우 MemberRepository는 스프링 컨텍스트에서 관리되는 빈이므로, 해당 빈을 자동으로 주입하여 테스트에서 사용할 수 있게 됩니다.  
 */
@RunWith(SpringRunner.class)  
@SpringBootTest  
public class MemberRepositoryTest {  
    @Autowired MemberRepository memberRepository;  

    // 테스트 코드에 Transactional을 걸어주면, 테스트 종료 후 DB를 롤백한다.
    // 만약, 롤백을 바라지 않는다면 @Rollback(false)를 추가하면 된다.
    @Test  
    @Transactional  
    // @Rollback(false)
    public void testMember() throws Exception {  
        // given (준비): 테스트를 위해 준비하는 과정으로, 테스트에 사용하는 변수, 입력 값 등을 정의  
        Member member = new Member();  
        member.setUsername("memberA");  
  
        // when (실행) : 실제로 액션을 하는 테스트를 실행  
        Long saveId = memberRepository.save(member);  
        Member findMember = memberRepository.find(saveId);  
  
        // then (검증) : 테스트를 검증하는 과정으로 예상한 값, 실제 실행을 통해 나온 값의 비교  
        assertThat(findMember.getId()).isEqualTo(member.getId());  
        assertThat(findMember.getUsername()).isEqualTo(member.getUsername());  

		// 같은 트랜잭션 내에서 저장 및 조회하는 것은 같은 영속성 컨텍스트를 가진다.
		// 따라서, 같은 영속성 컨텍스트 내에서 id값이 같으면 같은 Entity로 식별되는 것이다. 
		assertThat(findMember).isEqualTo(member);
		
    }  
}
```

- 잘 실행되는 것을 확인할 수 있다.
![[DAY3 테스트 성공.png]]

- 추가적으로, h2 데이터베이스를 보면 만들지도 않았던 MEMBER 테이블이 생성된 것을 볼 수 있다.
![[DAY3 DB 생성.png]]

- 왜냐하면, `application.yml`에서, ddl-auto문을 `create`로 지정했기 때문이다.
	- `drop` 문 후, `create`를 하는 과정을 거친다.
```yml
# ...
jpa:  
  hibernate:  
    ddl-auto: create
#...
```
![[DAY3 자동 Create.png]]


#### 4. 쿼리 파라미터 로그 남기기

- 