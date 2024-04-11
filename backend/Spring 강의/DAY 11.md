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
- 