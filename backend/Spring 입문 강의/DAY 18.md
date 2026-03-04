
#### 1. 스프링 통합 테스트 (1)

- 이번 Section에서는 스프링 컨테이너와 DB까지 연결한 통합 테스트를 진행해볼 것이다.
	- 테스트를 DB까지 다 연결해서 한다는 뜻이다.
	- 추가적으로, 순수 자바 코드로 테스트하는 것이 아니라, 스프링까지 싹 다 엮어서 해 볼 것이다.
	- 순차적으로 진행해보자.

- 먼저, test 파일명을 `MemberServiceIntrgrationTest.java` 파일을 만들어준 후, 아래 `join` 메소드에 대한 테스트를 실행해보자.
```java
package hello.hellospring.service;  
  
import hello.hellospring.domain.Member;  
import hello.hellospring.repository.MemberRepository;  
import hello.hellospring.repository.MemoryMemberRepository;  
import org.junit.jupiter.api.AfterEach;  
import org.junit.jupiter.api.BeforeEach;  
import org.junit.jupiter.api.Test;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.transaction.annotation.Transactional;  
  
import static org.assertj.core.api.Assertions.assertThat;  
import static org.junit.jupiter.api.Assertions.assertThrows;  
  
  
// 스프링이 테스트할 때는 @SpringBootTest, @Transactional 추가하면 됨  
@SpringBootTest  
@Transactional  
class MemberServiceIntegrationTest {  
  
    // 기존에는 직접 MemberService, MemberRepository 객체를 생성했음  
    // 이제 스프링 컨테이너에게, MemberService, MemberRepository를 받아야 함  
    // 그를 위해, 테스트할 때 바로 @Autowired 쓰면 됨  
    @Autowired MemberService memberService;  
  
    // MemoryMemberRepository -> MemberRepository로 변경  
    @Autowired MemberRepository memberRepository;  
  
    @Test  
    void join() {  
  
        // given  
        Member member = new Member();  
        member.setName("user1");  
  
        // when  
        Long saveId= memberService.join(member);  
  
        // then  
        Member findMember = memberService.findOne(saveId).get();  
        assertThat(member.getName()).isEqualTo(findMember.getName());  
    }  
  
    @Test  
    void dupMemberException(){  
  
        // given  
        Member member1 = new Member();  
        member1.setName("spring");  
  
        Member member2 = new Member();  
        member2.setName("spring");  
  
        // when   
memberService.join(member1); // 최초 저장은 문제 없어야 함  
  
        // then  
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));  
        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다."); 
    }  
}
```

- 통합 테스트(스프링 컨테이너와 DB까지 연결해서 테스트)는 단위 테스트(순수 자바 코드로만 테스트)와 다르게, 스프링이 실행되는 모습을 볼 수 있다.
![[테스트할 때 스프링 컨테이너를 실행하는 모습.png]]

- 이미 데이터베이스에는 `user1`이라는 name으로 추가된 row가 있으므로, 에러가 발생한다.
![[join문 실패.png]]

- 그럼, member table의 내용을 지워보자
```sql
/* 테이블의 모든 행 삭제 */
DELETE FROM MEMBER

/* 만약 테이블 자체를 삭제하고 싶다면? */
/* DROP TABLE MEMBER */
```

- 실행이 되었다.
![[join문 성공.png]]


#### 2. 스프링 통합 테스트 (2)

- 테스트는 반복 진행이 가능해야 한다.
	- 만약, 데이터베이스를 초기화하지 않고, `join()`메소드를 여러 번 테스트해야 하게 된다면, 위의 예제에서 user1이라는 name을 가진 유저가 계속해서 추가될 것이다. 
	- 그럼 반복을 위해 매번 데이터베이스에서 테스트 데이터를 지우는 쿼리를 작성해야 할 것이다. 

- `@Transactional` annotation을 테스트케이스에 달아 놓으면, 아래와 같은 흐름이 발생한다.
	1. 테스트를 실행할 때 트랜잭션을 실행한다.
	2. 테스트 과정에서, DB에 데이터를 Insert된다.
	3. 테스트가 끝나면 Rollback시킨다.

- 즉, 지우는 코드를 추가하지 않아도 된다는 것이다.

> [!note] 트랜잭션(Transaction)
> - 데이터베이스에서 여러 작업을 하나의 단위로 묶어서 처리하는 개념이다.
> - 데이터베이스의 상태를 일관성 있게 유지하기 위해 사용된다.
> - 아래의 ACID 속성을 준수한다.
> 	1. **원자성(Atomicity)**: 트랜잭션은 하나의 원자적인 단위로 처리되어야 한다. 즉, 모든 작업이 성공하거나 실패해야 하며. 실패 시 이전 상태로 롤백된다.
> 	2. **일관성(Consistency)**: 트랜잭션 전후에 데이터베이스는 일관된 상태를 유지해야 한다. 데이터베이스 제약 조건이나 규칙을 위배하지 않는다.
> 	3. **고립성(Isolation)**: 여러 트랜잭션이 동시에 실행되더라도 각 트랜잭션은 다른 트랜잭션의 작업에 영향을 받지 않고 독립적으로 실행된다.
> 	4. **지속성(Durability)**: 트랜잭션이 성공적으로 완료되면 그 결과는 영구적으로 저장되어야 한다. 즉, 시스템 장애가 발생해도 데이터는 유실되지 않아야 한다.

> [!note] Rollback
> - 트랜잭션이 실패하거나 명시적으로 롤백되었을 때, 해당 트랜잭션에서 수행한 모든 변경 사항을 **이전 상태로 되돌리는 것**을 의미한다. 
> - 이를 통해 데이터베이스를 이전의 일관된 상태로 되돌릴 수 있다. 
> - 롤백은 원자성을 유지하기 위한 중요한 개념으로, 트랜잭션 중간에 오류가 발생하거나 예외가 발생하면 자동으로 수행될 수 있다.

> [!note] **@SpringBootTest**:
> - 스프링 애플리케이션을 테스트하기 위해 사용되는 annotation
> - 이 annotation이 붙은 테스트 클래스는 스프링 애플리케이션 컨텍스트를 로드하고, 애플리케이션 내의 모든 구성 요소들을 테스트할 수 있도록 한다.
> 	- 즉, 이 annotation을 사용하면 실제 애플리케이션과 유사한 환경에서 테스트를 수행할 수 있다.
> 	- 왜냐하면, 스프링 컨테이너와 테스트를 함께 실행하기 때문이다.

> [!note] **@Transactional**
> - 테스트 메소드나 테스트 클래스에 부착하여 **트랜잭션 관리를 활성화**한다.
> - 일반적으로 테스트 메소드가 실행될 때 트랜잭션을 시작하고, 테스트가 완료되면 해당 트랜잭션을 롤백한다.
> 	- 이를 통해 각각의 **테스트 메소드가 독립적으로 실행**되고, 데이터베이스 상태가 변하지 않도록 보장한다.
> 	- 테스트 메소드에서 데이터베이스 조작을 수행하더라도, 실제 데이터베이스는 테스트 종료 후에 이전 상태로 롤백되어 테스트 간의 영향을 최소화한다.

- 따라서 `@SpringBootTest`와 `@Transactional`을 함께 사용하면 스프링 애플리케이션을 테스트할 때 테스트 메소드가 데이터베이스 트랜잭션 내에서 실행되어, 테스트 간의 데이터베이스 상태가 격리되고, 테스트가 완료된 후에는 데이터베이스가 롤백되어 초기 상태로 돌아가는 것을 보장할 수 있다.