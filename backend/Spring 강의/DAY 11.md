### 1. 스프링 빈을 등록하고, 의존 관계 설정하기

- 지금까지, Member 객체를 만들고, `MemberService`와 `MemoryMemberRepository` 등을 만들었다.
	- 그리고 서비스를 통해 회원가입을 진행했고, 그 과정에서 리포지토리 내 데이터를 저장하거나 불러오는 과정도 수행했다.
	- 또, 테스트도 진행했다.

- 기억이 안나면 다시보자 1. (`repository`)
	- **`repository set`**: `store.put(member.getId(), member);`
	- **`repository get`**: `Optional.ofNullable(store.get(id))`

- 기억이 안나면 다시보자 2. (`MemberServiceTest.java 중 dupMemberException)
```java
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
  
        // // 두번째 콜백으로 넘긴 함수가 실행되면서 1번째로 전달한 예외가 터져주는지 확인하는 구문  
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));  
//        assertThrows(NullPointerException.class, () -> memberService.join(member2)); // null pointer 에러가 터지는지 기대함 (아니면 테스트 실패)  
  
        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다."); // 에러 안나면 성공  
  
//        try{  
//            memberService.join(member2); // memberRepository에 "spring" 데이터가 있으니 오류 띄워야 함  
//            fail();  
//        } catch (IllegalStateException e){  
//            assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");  
//        }  
    }
```

- 클라이언트 측에서 볼 수 있는 화면을 붙이기 위해서는, **Controller**와 **View**가 있어야 한다. 
	- 사용자가 회원가입하고, 회원가입된 결과를 HTML로 뿌려줘야하니까...
	- 이렇게 하려면, 일단 Controller(예제에서는, `MemberController`)를 먼저 만들어야한다.

- 예제에서 사용할 `MemberController`는 우리가 원하는 동작을 하게끔 만들어주어야 한다.
	- `Memberservice`를 통해 회원가입하고, 데이터를 조회할 수 있는 기능
	- **이런 관계를 `MemberController`가 `MemberController`를 의존한다**고 표현한다.
	- 그리고 우리는 스프링에서 의존관계를 추가해 주어야 한다.


### 2. 컴포넌트 스캔과 자동 의존관계 설정

##### 1.  Annotation : `@Controller`
```java
package hello.hellospring.controller;  
  
import org.springframework.stereotype.Controller;  
  
@Controller  
public class MemberController {  
}
```

- `@Controller`라는 annotation이 있으면, 스프링은 동작할 때 `MemberController` 객체를 생성하여 가지고있는다.  
	- 이를, 스프링 컨테이너에서, spring bean이 관리된다고 표현한다.  

![[스프링부트 - api and responsebody.png]]
- 위 이미지와 같이, `@Controller`라는 annotation이 있으면 스프링이 실행될 때, 자기가 알아서 관리를 한다고 알아두자.
	- 이제 곧 만들어볼 `MemberController`도 annotation이 있으면, **스프링 컨트롤러**에 의해 관리된다.

```java
package hello.hellospring.controller;  
  
import hello.hellospring.service.MemberService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
  
@Controller
public class MemberController {  
    // 스프링이 관리를 하게되면, 스프링 컨테이너에 특정 요소를 다 등록하고, 이후 전부다 스프링 컨테이너에서 요소를 받아서 사용하여야한다.  
  
    // 여기서 사용하는 memberService는 여러 인스턴스를 생성할 필요가 없다. (하나만 생성해놓고 공용으로 쓰면됨)  
    // private final MemberService memberService = new MemberService();
    // 이제, 스프링 컨테이너에 MemberService 인스턴스를 1개만 등록해보자.  
    private final MemberService memberService;
  
  
    // 생성자에 @Autowired가 있으면, 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다.  
    // 이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI(Dependency Injection), 의존성 주입이라 한다.  
    // 이전 테스트에서는 개발자가 직접 주입했고, 여기서는 @Autowired에 의해 스프링이 주입해준다.  
    @Autowired  
    public MemberController(MemberService memberService){  
  
        // 1. 스프링은 동작할 때 MemberController 객체를 생성한다.  
        // 2. 이 때, 이 생성자를 호출한다.  
        // 3. 생성자에 @Autowired이 있으면, memberService를 스프링이 스프링 컨테이너에 있는 memberService를 가져와서 연결시켜준다.
        this.memberService = memberService;  
    }



  
}
```
- `MemberController`에서는 여러 인스턴스를 생성할 필요가 없다. (하나만 생성해놓고 공용으로 쓰면됨)
	- 따라서, `private final MemberService memberService = new MemberService();`와 같이 사용하면 안된다.

- 생성자에 `@Autowired`가 있으면, 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다.  
	- 이렇게 **객체 의존관계**를 외부에서 넣어주는 것을 **DI(Dependency Injection)** - [의존성 주입]이라 한다.  
	- 이전 테스트에서는 개발자가 직접 주입했고, 여기서는 `@Autowired`에 의해 스프링이 주입해준다.

- 하지만, 위의 코드는 실행되지 않는다.
![[Pasted image 20240411134152.png]]

##### 2. Spring bean 등록

![[Pasted image 20240411134500.png]]
- `MemberService`는 그냥 **순수한 자바 클래스**이다. 
	- `MemberController`는 annotation이 있으니, 스프링이 동작할 때  **스프링 컨트롤러**에 의해 관리되는 규칙이 있으나, `MemberService`는 그런 것이 없다.
	- 그래서, 스프링이 실행될 때 스프링 컨테이너에 MemberController만 올라오고, MemberService는 안올라오는 것이다.

- 그럼 어떻게 해야할까? 
	- `@Service`라는 annotation을 서비스 클래스인 `MemberService` 위에 달아주면 된다.
```java
package hello.hellospring.service;  
  
import hello.hellospring.domain.Member;  
import hello.hellospring.repository.MemberRepository;  
import hello.hellospring.repository.MemoryMemberRepository;  
import org.springframework.stereotype.Service;  
  
import java.util.List;  
import java.util.Optional;  
  
// 서비스 클래스는 굉장히 비즈니스와 어울리는 기능과 네이밍을 가짐  
// 반면 리포지토리 클래스는 단순히 저장소에 넣었다 뺐다 하는 기능과 네이밍을 가짐  
@Service  
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
     */    public Long join(Member member) {  
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
	    memberRepository.findByName(member.getName()).ifPresent(m -> {  
            throw new IllegalStateException("이미 존재하는 회원입니다.");  
        });  
    }  
  
}
```

- 이 작업을 리포지토리에도 해주자.
```java
package hello.hellospring.repository;  
  
import hello.hellospring.domain.Member;  
import org.springframework.stereotype.Repository;  
  
import java.util.*;  

@Repository  
public class MemoryMemberRepository implements MemberRepository {  
  
    // 회원 정보를 메모리에 저장하기 위해 선언  
    // key는 회원의 아이디(Long), value값은 Member 타입임  
    // 해당 코드는 동시성 문제 발생  
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
        return store.values().stream()  
                .filter(member -> member.getName().equals(name))  
                .findAny();  
    }  
  
    public void clearStore() {  
        store.clear();  
    }  
}
```

- 컨트롤러로 **외부 요청**을 받고, 서비스에서 비즈니스 로직을 만들고, 리포지토리에서 데이터를 저장하는 과정은 **정형화된 과정**이다.

![[Pasted image 20240411144646.png]]
- 스프링 빈 등록 이미지를 보고, 과정을 이해해보자.
	1. 컨트롤러와 서비스를 연결시켜줘야한다. 
		- 컨트롤러에서 생성자에 `@Autowired`를 쓰면 `MemberController`가 생성될 때, Spring Bean에 등록되어 있는 `MemberService` 객체를 가져와 연결해준다. 
		- 이게, Depend