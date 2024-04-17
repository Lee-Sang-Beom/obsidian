
#### 1. 스프링 통합 테스트

- 이번 Section에서는 스프링 컨테이너와 DB까지 연결한 통합 테스트를 진행해볼 것이다.
	- 테스트를 DB까지 다 연결해서 한다는 뜻이다.
	- 추가적으로, 순수 자바 코드로 테스트하는 것이 아니라, 스프링까지 싹 다 엮어서 해 볼 것이다.

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
