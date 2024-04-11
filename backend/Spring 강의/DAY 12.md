
### 1. 자바 코드로 직접 스프링 빈 등록하기

- 컴포넌트 스캔 방법 말고, 직접 스프링 빈을 등록해보자.
	- 먼저, 회원 서비스와 회원 리포지토리의 `@Service, @Repository, @Autowired`등의 annotation을 지우고 시작해야 한다.

- 지웠다면, `hello.hellospring` 하위 `main`함수와 공통된 경로에 `SpringConfig.class` 파일을 만들어주자.

```java
package hello.hellospring;  
  
import hello.hellospring.repository.MemberRepository;  
import hello.hellospring.repository.MemoryMemberRepository;  
import hello.hellospring.service.MemberService;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
// 스프링이 실행될 때, Configuration을 먼저 읽고, config 내부의 @Bean annotation으로 설정된 요소를 스프링이 스프링 컨테이너에 스프링 빈으로 등록한다.  
@Configuration  
public class SpringConfig {  
  
    // @Bean: 스프링 빈을 등록할 것이라는 annotation  
    @Bean  
    public MemberService memberService(){  
        // memberSerivce 인스턴스를 반환하여, memberSerivce 인스턴스를 스프링 빈으로 등록 (스프링 컨테이너에)  
        // 이 때, MemberSerivce 인스턴스를 생성하기 위해서, MemberService의 생성자는 mmemoryMemberRepoitory 인스턴스를 요구한다.  
        return new MemberService(memberRepository());  
    }  
    @Bean  
    public MemberRepository memberRepository(){  
        // 구현체를 리턴해야함. 인터페이스 넣으면 안됨  
        return new MemoryMemberRepository();  
    }  
}
```

- 스프링이 실행될 때 `@Configuration` annotation이 있다면, 스프링은 이를 먼저 읽는다.
	- 그 다음, config 내부의 `@Bean` annotation으로 설정된 요소를 만나면, 스프링은 이 요소를 스프링 컨테이너에 스프링 빈으로 등록한다.

- 본 예제에서는 MemberController가 MemberService를 의존하고, MemberService가 MemberRepository를 의존한다.
	- 따라서, 생성해야 할 Bean은 `MemberService`, `MemberRepository`이다.

![[스프링 빈 등록 이미지.png]]

- 이렇게 하면 스프링이 처음 뜰 때,  `@Configuration` annotation을 확인하고, `@Bean` annotation으로 설정된 `memberService, memberRepository`를 스프링 컨테이너에 스프링 빈으로 등록한다.
	- 본 예제에서는, `memberService` Bean을 만들기 위해 필요한 `memberRepository`의 구현체인 `memorymemberRepository`를 `new memberService()`의 인자로 넘겨준다.
	- 이 부분이 컴포넌트 스캔의 `Autowried`와 유사하다.
	
- 그렇게 딱 위 이미지의 그림이 완성되었다.

```java
package hello.hellospring.controller;  
  
import hello.hellospring.service.MemberService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
  
// controller라는 annotation이 있으면, 스프링은 동작할 때 MemberController 객체를 생성하여 가지고있는다.  
// 이를, spring container에서, spring bean이 관리된다고 표현한다.  
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
- 이 때, 컨트롤러(`MemberController`)는 아래와 같이 컴포넌트 스캔 방식을 유지해 주어야한다. 
	- 어딘가에서 
