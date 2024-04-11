
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

- 스프링이 실행될 때, @Configuration을 먼저 읽고, config 내부의 @Bean annotation으로 설정된 요소를 스프링이 스프링 컨테이너에 스프링 빈으로 등록한다.