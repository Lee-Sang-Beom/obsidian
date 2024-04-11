
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
  
@Controller  
public class MemberController {  
  
.  
    private final MemberService memberService;  
  
    @Autowired  
    public MemberController(MemberService memberService){  
        this.memberService = memberService;  
    }  
  
}
```
- 이 때, 컨트롤러(`MemberController`)는 아래와 같이 컴포넌트 스캔 방식을 유지해 주어야한다. 
	- `MemberController`는 어딘가에서 `@Autowired`에 의해 스프링 컨테이너에 스프링 빈으로 저장되어, 불러와져야하는 요소가 아니다.

- 여기서는 `MemberController`의 생성자 구문에서 `@Autowired`를 지정했으니, 스프링 컨테이너에서 스프링 빈으로 관리되고 있는 `memberService`를 가져와 넣어준다.