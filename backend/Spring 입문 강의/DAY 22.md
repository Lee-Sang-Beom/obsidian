
#### 1. AOP가 필요한 상황

- 모든 메소드의 호출 시간을 측정하고 싶다면?
- 공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern)
- 회원 가입 시간, 회원 조회 시간을 측정하고 싶다면?

![[AOP가 필요한 상황.png]]


#### 2. MemberService 회원 조회 시간 측정

- 기존, `MemberService.java` 파일에서, `join()`메소드의 실행 시간을 측정해야 한다고 해서, 아래와 같이 timeMs를 출력했다고 가정하자.

```java
public Long join(Member member) {  
	// start
    long start = System.currentTimeMillis();  
    try{  
        validateDuplicatedMember(member);  
        memberRepository.save(member);  
        return member.getId();  
    } finally {
	    // end  
        long finish = System.currentTimeMillis();  
        long timeMs = finish - start;
        // println  
        System.out.println("join = "+ timeMs + "ms");  
    }  
}
```

- 전체 회원 조회도 똑같이 해보자.
```java
public List<Member> findMembers() {  
    long start = System.currentTimeMillis();  
    try {  
        return memberRepository.findAll();  
    } finally {  
        long finish = System.currentTimeMillis();  
        long timeMs = finish - start;  
        System.out.println("findMembers " + timeMs + "ms");  
    }  
}
```

- 정상적으로 로그가 잘 출력된다.
![[AOP 없이 findMembers 사용.png]]

- 이제, 아래의 문제 사항이 있다.
	1. 회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.
		- 핵심 비즈니스 로직 자체는 **핵심 관심 사항**이라 할 수 있다.
	
	2. 시간을 측정하는 로직은 공통 관심사항이다.
		- 공통적으로 여러 메소드에 대해 시간을 측정하는 로직이 있다고 가정하자. 이 때, 시간을 측정하는 로직은 **공통 관심 사항**이라 부를 수 있다.
	
	3. 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
	4. 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
		- 전체적인 메소드를 공통으로 만들기는 어렵다. (하나씩 찾아가야 함)
	
	5. 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.


#### 3. AOP (Aspect Oriented Programming)

- AOP(Aspect-Oriented Programming)는 소프트웨어 공학에서 사용되는 프로그래밍 패러다임 중 하나이다.
	- AOP는 프로그램을 여러 관심사로 분리하여 개발하는 방법을 제공한다.

- 전통적인 객체지향 프로그래밍에서는 클래스와 객체를 중심으로 코드를 구성하며, 각 객체는 자신의 역할에 따라 코드를 구현한다.
	- 그러나 **프로그램이 복잡해지면서 특정 관심사(로깅, 보안, 트랜잭션 처리 등)가 여러 객체에 걸쳐 중복되거나 분산되는 문제가 발생**하기 시작했다.
	- 이로 인해 코드의 가독성과 유지보수성이 저하되는 문제가 생겼다.

- AOP는 이러한 문제를 해결하기 위해 등장했다. AOP는 **관점(Aspect)을 기반**으로 프로그램을 설계하며, 각 관점은 프로그램의 여러 부분에서 발생하는 특정 관심사를 나타낸다.
	- 이러한 관점은 프로그램의 핵심 로직과는 분리되어 관심사에 따라 모듈화된다.
	- 그리고, AOP에서는 이 **관점**을 **cross-cuttong concern(공통 관심 사항)** 이라고 부른다. 
		- 이  **cross-cuttong concern**는 코드의 여러 지점에서 발생하는데, 예를 들어 로깅, 보안, 트랜잭션 처리 등이 있을 수 있다.

- AOP를 사용하면 횡단 관심사를 각 모듈에서 분리하여 중복을 줄이고, 코드의 재사용성과 유지보수성을 향상시킬 수 있다. 
	- AOP를 구현하기 위한 여러 기술과 프레임워크가 있으며, 대표적으로는 Spring Framework의 AOP 모듈이 있다.
	- 이를 통해 메소드 실행 전후에 특정 동작을 추가하거나 예외를 처리하는 등의 작업을 할 수 있다.

- 그럼, 공통 관심사항(cross-cutting concern)과 핵심 관심사항(core concern)을 분리시킬 수 있는 AOP를 적용해보자.


#### 4. 적용하기 

![[AOP 적용.png]]
- 그럼 지금부터, 시간 측정 로직을 한 군데 모아놓고 **내가 원하는 곳에 지정**하는 방법을 알아보자.

```java
package hello.hellospring.aop;  
  
import org.aspectj.lang.ProceedingJoinPoint;  
import org.aspectj.lang.annotation.Around;  
import org.aspectj.lang.annotation.Aspect;  
import org.springframework.stereotype.Component;  
  
@Aspect  
@Component  
public class TimeTraceAop {  
    @Around("execution(* hello.hellospring..*(..))")  
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {  
        long start = System.currentTimeMillis();  
        System.out.println("START: " + joinPoint.toString());  
        try {  
            return joinPoint.proceed();  
        } finally {  
            long finish = System.currentTimeMillis();  
            long timeMs = finish - start;  
            System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");  
        }  
    }  
}
```

1. `@Aspect` : `@Aspect` annotation을 사용하면, `TimeTraceAop` 클래스가 Aspect로 사용됨을 나타내고 있다.

2. `@Component` `@Component` annotation은 이 클래스가 스프링 컨테이너에 스프링 빈으로 등록될 것임을 나타낸다.

3. `@Around("execution(* hello.hellospring.*(..))")`: **Advice**를 적용할 대상을 지정한다.
	- Advice를 적용할 메소드를 지정하는 포인트컷(Pointcut) 표현식으로, 이 표현식은 Aspect를 적용할 메소드를 선별하기 위해 사용된다.

```null
>> execution(* hello.hellospring..*(..))

1. `*`: 리턴 타입을 나타내며, 여기서는 어떤 리턴 타입이든 상관없다는 것을 의미한다.

2. `hello.hellospring..*`: 메소드가 속한 클래스의 패키지 경로를 나타낸다.
  > `..` : `..`은 현재 패키지와 그 하위 패키지를 의미한다.
  > `hello.hellospring..`:  `hello.hellospring..`는 `hello.hellospring` 패키지와 그 하위 패키지까지의 범위를 의미한다.
  > `*`: 그리고 뒤에 '*'기호는 해당 범위까지의 클래스를 의미한다.
  > hello.hellospring..*`: 따라서 `hello.hellospring..*`는 `hello.heelospring` 패키지와, 그 하위 패키지까지의 범위에 있는 "클래스"를 포함한다는 뜻이다.
  
3. `*(..)`: 메소드 이름과 파라미터를 나타낸다.
  > `*`는 모든 메소드 이름을 의미하고, `(..)`는 모든 파라미터를 의미한다.
  > 따라서 이 부분은 모든 메소드를 대상으로 한다는 것을 의미한다.
```

4. `execute`: `execute` 메소드는 Advice 역할을 한다.
	- Advice에는 언제, 어디서, 어떻게 Aspect를 적용할지를 정의한다. 
	- 여기서는 `@Around` annotation을 사용하여 메소드 실행 전후에 Aspect를 적용하겠다고 선언하였다.

- 참고로, **execution** 명시자는 아래와 같은 규칙을 보유한다.

>  [참고자료 명시자 구조 이미지 >> hstory0208님의 포스트](https://hstory0208.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-AOP-Pointcut-%ED%91%9C%ED%98%84%EC%8B%9D)
>  [참고자료 명시자 설명 및 예시 이미지 >> ITisTrue님의 포스트](https://ittrue.tistory.com/233)

![[execution 명시자 규칙.png]]
 ![[execution 명시자.png]]