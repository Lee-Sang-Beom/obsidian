### 회원 관리 예제 - 백엔드 개발

- 비즈니스 요구사항 정리
- 회원 도메인과 리포지토리 만들기 
- 회원 리포지토리 테스트 케이스 작성
- 회원 서비스 개발
- **회원 서비스 테스트 (여기)**


### 2. 작성 (1). MemberSevice

- `(main/java/hello.hellospring/service/MemberService.class)
`
```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;

import java.util.List;
import java.util.Optional;

// 서비스 클래스는 굉장히 비즈니스와 어울리는 기능과 네이밍을 가짐
// 반면 리포지토리 클래스는 단순히 저장소에 넣었다 뺐다 하는 기능과 네이밍을 가짐
public class MemberService {
    private final MemberRepository memberRepository;

    // 내부에서 MemberRepository를 new로 생성하는게 아니라, 외부에서 넣어주도록 변경
    // 이것을 dependency injection(DI)라고 함
    public MemberService(MemberRepository memberRepository){
        this.memberRepository = memberRepository;
    }
    /**
     * 회원가입
     *  - 리포지토리에 member 저장 (단, 같은 이름의 중복회원이 있으면 안된다)
     *  - member의 id 반환 (임의)
     */
    public Long join(Member member) {
        validateDuplicatedMember(member); // 중복회원 검증
        memberRepository.save(member);
        return member.getId();
    }

    /**
     * 전체회원 조회
     */
    public List<Member> findMembers(){
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId){
        return memberRepository.findById(memberId);
    }

    /**
     * 아이디에 해당하는 유저 반환
     */
    public Optional<Member> findMember(Long memberId){
        return memberRepository.findById(memberId);
    }

    /**
     * 같은 이름의 중복 회원이 있는지 먼저 검사
     */
    private void validateDuplicatedMember(Member member) {
        /**
         * isPresent(): Optional 객체가 값을 포함하고 있는지 여부를 확인하는 데 사용된다. 반환 값은 boolean 형식이며, 값이 존재하면 true를 반환하고, 값이 존재하지 않으면 false를 반환한다.
         * ifPresent(): Optional 클래스는 값이 존재하거나 존재하지 않을 수 있는 컨테이너를 나타내며, 해당 메서드는 Optional 객체가 값을 포함할 때만 지정된 로직을 실행하도록 하는 메서드이다.
         */
        memberRepository.findByName(member.getName()).ifPresent(m -> {
            /**
             * IllegalArgumentException: null이 아닌 인자의 값이 잘못되었을 때
             * IllegalStateException(이거!): 객체 상태가 메서드 호출을 처리하기에 적절치 않을 때
             * NullPointException: null 값을 받으면 안 되는 인자에 null이 전달되었을 때
             * IndexOutOfBoundsException: 인자로 주어진 첨자가 허용 범위를 벗어났을 때
             * ConcurrentModificationException: 병렬적 사용이 금지된 객체에 대한 병렬 접근이 탐지 되었을 때
             * UnsupportedOperationException: 객체가 해당 메서드를 지원하지 않을 때
             */
            throw new IllegalStateException("이미 존재하는 회원입니다.");
        });
    }

}

```


### 2. 작성 (2). MemberServiceTest 

- `(test/java/hello.hellospring/service/MemberServiceTest.class)`

```java
package hello.hellospring.service;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
class MemberServiceTest {

    // 헷갈리지 말아야 할것 (from 커뮤니티)
    // repository의 경우 실제 DB와 밀접하게 통신하는 레이어(계층)이라고 보시면 됩니다.

    // Service의 경우, 애플리케이션의 비즈니스 로직을 처리하는 Layer입니다.
    // 가령, 회원가입을 할 때 회원가입(Join)에서 실명 확인 서비스 api를 통해 검증된 사람만 회원가입을 해야된다라는 요건이 추가되었다고 가정해보겠습니다.
    // 이 때, 이 실명 확인 서비스는 Repository Layer가 아닌, Service에서 처리가 되어야 하는 로직입니다.

    MemberService memberService;

    // memberService 안에서 사용되는 MemoryMemberRepository와 다른 객체임. 그래서 이거 말고 , memberService 안에서 사용되는 MemoryMemberRepository를 사용할 것임
    // MemoryMemberRepository memberRepository = new MemoryMemberRepository();
    MemoryMemberRepository memberRepository;

    // 동작하기 전에 실행되는 구문
    // 테스트할때마다 beforeEach가 생성됨
    @BeforeEach
    public void beforeEach(){
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }


    // 테스트가 1개가 끝나면, 깔끔하게 리포지토리 데이터를 clear해줘야함
    // 테스트 순서는 항상 동일하지 않으므로, 서로 의존적이면 안됨
    @AfterEach
    public void afterEach(){
        memberRepository.clearStore();
    }

    @Test
    void join() {
        // 회원가입

        // given
        Member member = new Member();
        member.setName("spring");

        // when
        Long saveId= memberService.join(member);

        // then
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());
    }

    @Test
    void dupMemberException(){
        // 중복회원예외

        // given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");

        // when 
        memberService.join(member1); // 최초 저장은 문제 없어야 함

        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2)); // 두번째 콜백으로 넘긴 함수가 실행되면서 1번째로 전달한 예외가 터져주는지 확인하는 구문
//        assertThrows(NullPointerException.class, () -> memberService.join(member2)); // null pointer 에러가 터지는지 기대함 (아니면 테스트 실패)


        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다."); // 에러 안나면 성공



//        try{
//            memberService.join(member2); // memberRepository에 "spring" 데이터가 있으니 오류 띄워야 함
//            fail();
//        } catch (IllegalStateException e){
//            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
//        }
    }

    @org.junit.jupiter.api.Test
    void findMembers() {
    }

    @org.junit.jupiter.api.Test
    void findMember() {
    }
}
```