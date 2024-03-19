### 회원 관리 예제 - 백엔드 개발

- 비즈니스 요구사항 정리
- **회원 도메인과 리포지토리 만들기 (여기)** 
- 회원 리포지토리 테스트 케이스 작성
- 회원 서비스 개발
- 회원 서비스 테스트

---

### 회원 도메인 만들기

- `(main/java/hello.hellospring/domain/Member.class)`

```java
package hello.hellospring.domain;

// 회원 도메인 만들기(회원 객체)
public class Member {
    // 데이터: 회원 아이디, 회원 이름
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---

### 회원 리포지토리 인터페이스 만들기

- `(main/java/hello.hellospring/repository/MemberRepository.interface)`

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import java.util.List;
import java.util.Optional;

// 회원 리포지토리 인터페이스: 회원 객체를 저장하는 저장소
public interface MemberRepository {

    // 회원 저장: 저장 시, 저장한 회원정보 반환
    Member save(Member member);

    // 아이디로 회원 찾는 걸 만듬
    // (Optional: findById로 유저를 못찾고 null이 반환될 수 있음. Optional이라는 것으로 null을 감싸는 방법임)
    Optional<Member> findById(Long id);

    // 이름으로 회원 찾는 걸 만듬
    Optional<Member> findByName(String name);

    List<Member> findAll();

}
```

---

### 메소드 정의하기

- `(main/java/hello.hellospring/repository/MemoryMemberRepository.class)`

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import java.util.*;

public class MemoryMemberRepository implements MemberRepository {

    // 회원 정보를 메모리에 저장하기 위해 선언
    // key는 회원의 아이디(Long), value값은 Member 타입임
    // 해당 코드는 동시성 문제 발생
    // 즉, store라는 Map 타입의 클래스 변수가 저장소 역할을 함
    private static Map<Long, Member> store = new HashMap<>();

    // seq: 0,1,2,...로 키값을 생성함.
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {

        // 멤버값에 id 세팅: seq값을 먼저 올린 값을 id로 저장
        member.setId(++sequence);

        // 저장소(맵객체) 에 memberId(key), member(value) 저장
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        // 아이디 찾기(꺼내기)

        // 결과가없으면 null로 나올수있음.
        // return stort.get(id)만을 쓰기보다는 이것을, ofNullable로 감싸주면, null을 Optional로 감싸서 반환됨
        // (클라이언트가 뭔가를 할 수 있게 됨)
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public List<Member> findAll() {

        // store의 모든 값들을 array형태로 반환
        return new ArrayList<>(store.values());
    }

    @Override
    public Optional<Member> findByName(String name) {
        // filter => name이 같은 걸 찾아서 반환한다.
        // 끝까지 돌렸는데도 못찾으면 Optional이 null을 감싸서 반환
        // findAny는 하나의 회원만 반환한다.
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    public void clearStore() {
        store.clear();
    }
}
```
