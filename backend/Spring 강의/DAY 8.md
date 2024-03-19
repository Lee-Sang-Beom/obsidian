### 회원 관리 예제 - 백엔드 개발

- 비즈니스 요구사항 정리
- 회원 도메인과 리포지토리 만들기 
- **회원 리포지토리 테스트 케이스 작성 (여기)**
- 회원 서비스 개발
- 회원 서비스 테스트

---

### 테스트 케이스

- 개발 기능을 테스트하기위해, 자바의 **main() 메소드나, 웹 애플리케이션의 컨트롤러**를 통해 해당 기능을 실행할 수 있다.
- 하지만, 이런 방법은 테스트 세팅과 실행이 오래걸린다. 또한 반복이 어렵고, 여러 테스트를 한 번에 실행하기 어렵다는 단점이 있다.
- 자바에서는 `JUnit`이라는 테스트 프레임워크를 통해 테스트를 진행해 볼 수 있다.

- 테스트 코드 작성 시, **테스트는 각각 독립적으로 실행**되어야 한다는 원칙을 지켜주는 것이 좋다.
- 테스트 순서에 의존관계가 있다면, 테스트 실행 방법이나 환경에 따라 테스트 결과가 달라지고 테스트 대상이 불분명해지기 때문이다.


---

### 작성 1. MemoryMemberRepositoryTest

- `(test/java/hello.hellospring/repository/MemoryMemberRepositoryTest.class)`

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.assertj.core.api.Assertions.*;
//테스트 케이스는 src/test/java 하위 폴더에 생성한다

public class MemoryMemberRepositoryTest {
    MemoryMemberRepository repository = new MemoryMemberRepository();

    // 테스트가 1개가 끝나면, 깔끔하게 리포지토리 데이터를 clear해줘야함
    // 테스트 순서는 항상 동일하지 않으므로, 서로 의존적이면 안됨
    @AfterEach
    public void afterEach(){
        repository.clearStore();
    }

    @Test
    public void save(){
        // 해당 로직은 리포지토리의 save 메서드가 잘 동작하는지를 확인하는 코드

        // given (준비): 테스트를 위해 준비하는 과정으로, 테스트에 사용하는 변수, 입력 값 등을 정의
        Member member = new Member();
        member.setName("testMember1");

        // when (실행) : 실제로 액션을 하는 테스트를 실행
        repository.save(member);

        // then (검증) : 테스트를 검증하는 과정으로 예상한 값, 실제 실행을 통해 나온 값의 비교
        // optional로 감싼건 get()을 한번 더해서 optional 안의 값에 접근할 수 있다.
        // 직접 바로 꺼내는 건 권장하지 않는 편.
        Member resultMember = repository.findById(member.getId()).get();

        // AssertJ는 가독성이 좋고 다양한 검증 기능을 제공하는 라이브러리이다.
        // 두 테스트 케이스에서 assertThat을 사용하고 있으며, 이는 AssertJ 라이브러리의 메서드입니다. assertThat 메서드를 사용하면 테스트 코드가 보다 가독성이 좋아지며, 다양한 검증 메서드를 사용하여 테스트를 보다 쉽게 작성할 수 있습니다.
        // 테스트에서 예상한 결과(member)와 실제 결과(resultMember)가 동일한지를 검증하는 구문

        assertThat(resultMember).isEqualTo(member);
        // assertThat(resultMember).isEqualTo(member);
        // System.out.println("Result is "+ (resultMember == member));

    }

    @Test
    public void findByName(){
        // 해당 로직은 리포지토리의 findByName 메서드가 잘 동작하는지를 확인하는 코드

        // given
        Member member1 = new Member();
        member1.setName("findNameMember1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("findNameMember2");
        repository.save(member2);

        // when
         Member resultMember = repository.findByName(member1.getName()).get();
         System.out.println("테스트 결과" + resultMember);
        // Member resultMember = repository.findByName(member2.getName()).get();

        // then
        assertThat(resultMember).isEqualTo(member1);
    }

    @Test
    public void findAll(){
        // 해당 로직은 리포지토리의 findAll 메서드가 잘 동작하는지를 확인하는 코드

        // given
        Member member1 = new Member();
        member1.setName("findAllMember1");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("findAllMember2");
        repository.save(member2);

        // when
        List<Member> resultMemberList = repository.findAll();

        // then
        assertThat(resultMemberList.size()).isEqualTo(2);

    }
}


```