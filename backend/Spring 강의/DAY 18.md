
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

> [!note] 
> - 위에서 여럿 말했듯, CORS 정책에 의하여, Origin을 비교하는 작업은 서버 측의 스펙이 아닌, 브라우저 측의 스펙이다.
> - 따라서, 서버에서 정상적으로 200 status code를 보내도, 브라우저에서 이 응답이 CORS 정책을 위반했다고 분석할 수 있는 것이다.
> 	- 서버 측 로그에서는 정상 응답했다는 로그만 남으므로, CORS를 정확히 이해해야 이 Error를 해결할 수 있다.