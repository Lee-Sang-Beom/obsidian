
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
>> execution(* hello.hellospring..*.*(..))

1. `*`: 리턴 타입을 나타내며, 여기서는 어떤 리턴 타입이든 상관없다는 것을 의미한다.

2. `hello.hellospring..*`: 메소드가 속한 클래스의 패키지 경로를 나타낸다.
  > `..` : `..`은 현재 패키지와 그 하위 패키지를 의미한다.
  > `hello.hellospring..`:  `hello.hellospring..`는 `hello.hellospring` 패키지와 그 하위 패키지까지의 범위를 의미한다.
  > `*`: 그리고 뒤에 '*'기호는 해당 범위까지의 클래스를 의미한다.
  > hello.hellospring..*`: 따라서 `hello.hellospring..*`는 `hello.heelospring` 패키지와, 그 하위 패키지까지의 범위에 있는 "클래스"를 포함한다는 뜻이다.
  
3. `*(..)`: 메소드 이름과 파라미터를 나타낸다.
  > `*`는 모든 메소드 이름을 의미하고, `(..)`는 모든 파라미터를 의미한다.
  > 따라서 이 부분은 모든 메소드를 대상으로 한다는 것을 의미한다.
  
---

그럼 아래는 뭘까?

>> execution(* hello.hellospring.service..*.*(..))

1. 모든 리턴 타입을 대상으로 한다.
2. `hello.hellospring.service`경로를 기본으로 하되, `..`를 지정함으로써 `hello.hellospring.service` 패키지와 그 하위 패키지 경로까지를 execution의 실행범위에 포함시킨다는 뜻이다.

- 추가적으로, *.*가 있는데, 그 경로 하위에 있는 [클래스(*).메소드(*)]에 대하여 모든 것을 대상으로 한다고 지정하였다.

3. (..)는 메소드의 파라미터 타입을 나타낸다. ".."는 0개 이상을 파라미터로 받는 메소드를 대상으로 한다는 뜻이다.
```

4. `execute`: `execute` 메소드는 Advice 역할을 한다.
	- Advice에는 언제, 어디서, 어떻게 Aspect를 적용할지를 정의한다. 
	- 여기서는 `@Around` annotation을 사용하여 메소드 실행 전후에 Aspect를 적용하겠다고 선언하였다.
		- execution 실행 경로에 정의된 메소드가 실행될 때마다 중간에서 인터셉트가 발생한다고 생각하자.
	
	- AOP로 호출될 때마다 Advice 역할을 하는 메소드는 `ProceedingJoinPoint joinPoint`를 전달받는다. 
		- 이것을 가지고, 특정 메소드를 계속 실행시킬 수도 있고 실행을 멈춰줄 수도 있다.

- 참고로, **execution** 명시자는 아래와 같은 규칙을 보유한다.

>  [참고자료 명시자 구조 이미지 >> hstory0208님의 포스트](https://hstory0208.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-AOP-Pointcut-%ED%91%9C%ED%98%84%EC%8B%9D)
>  [참고자료 명시자 설명 및 예시 이미지 >> ITisTrue님의 포스트](https://ittrue.tistory.com/233)


#### 참고: execution 명시자
![[execution 명시자 규칙.png]]
 ![[execution 명시자.png]]

> AOP 실행 결과 

![[AOP 실행결과.png]]


#### 5. 프로젝트 적용 예시 살펴보기

```java
@Pointcut("execution(* egovframework.deps.*.controller.*.*(..)) || execution(* egovframework.deps.areas.company.controller.*.*(..))" )  
public void webControllerPointcut() {  
}  
  
@Around("webControllerPointcut()")  
public Object doLogging(ProceedingJoinPoint joinPoint) throws Throwable {  
  
try {  
  
    HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.currentRequestAttributes()).getRequest();  
  
    String controllerName = joinPoint.getSignature().getDeclaringType().getName();  
    String methodName = joinPoint.getSignature().getName();  
  
  
    logger.info("□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□  REQUEST LOG CONSOLE START □□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□□");  
    logger.info("ControllerName :{}", controllerName);  
    logger.info("MethodName :{}", methodName);

	// ...
  
    } catch (Exception e) {  
        log.error("LoggerAspect error", e);  
    }  
  
    return joinPoint.proceed();  
  
}
```

1. `@Pointcut` : 해당 annotation은 포인트컷(Pointcut)을 정의한다. 
	- 포인트컷은 어드바이스(Advice)가 적용될 대상을 지정하는데 사용된다.
	- 위의 코드에서는 두 개의 execution 표현식을 포인트컷으로 사용하여 `webControllerPointcut()` 메소드를 정의했다.
    
2. `@Around` 어노테이션은 Advice를 정의한다.
	- Advice는 포인트컷에 정의된 대상 메소드를 감싸서 실행 전후에 추가 동작을 수행한다.
	- 위의 코드에서는 `doLogging()` 메소드가 Around Advice를 정의한다.
    
3. `doLogging()` 메소드는 `webControllerPointcut()` 포인트컷에 정의된 대상 메소드를 실행하기 전후에 로깅 작업을 수행한다.
	- `doLogging`은 AspectJ에서 Aspect에 의해 적용되는 Advice(조언)의 일종이다.
		- 이 메소드는 `@Around` 어노테이션이 지정된 메드로, Around Advice로 분류된다.

	- `ProceedingJoinPoint` 매개변수는 Advice가 적용된 메서드를 호출하기 위해 사용된다. 이 객체를 통해 Advice가 적용된 메서드를 호출하고, 해당 메서드의 실행을 제어할 수 있다.

	- 일반적으로 `ProceedingJoinPoint.proceed()` 메서드를 호출하여 Advice가 적용된 메서드를 실행한다.
		- 이를 호출하면 Advice가 적용된 메서드의 실행이 시작되며, 반환 값은 Advice가 적용된 메서드의 반환 값이 된다.

	- 따라서 `doLogging` 메서드는 Advice가 적용된 메서드의 실행 전후에 특정한 로깅 작업이나 추가적인 작업을 수행할 수 있다.
		- 이를 통해 비즈니스 로직과는 별도로 Cross-cutting concern(관점 지향)을 구현할 수 있다.

	- 여기서는 HTTP 요청의 URI, 메소드, 요청 파라미터 등을 로깅하는 것으로 보인다.
    
4. `joinPoint.proceed()` 메소드는 실제 대상 메소드를 호출한다.
	- 이 메소드 호출 전후에 로깅이 수행되는 것을 확인할 수 있다.


#### 6. 스프링의 AOP 동작 방식

- 지금까지, AOP를 사용하지 않아 문제가 되었던 아래와 같은 로깅 사항이 해결되었다.
	1.  회원가입, 회원 조회 등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다.
	2.  시간을 측정하는 로직을 별도의 공통 로직으로 만들었다. 
	3.  핵심 관심 사항을 공통 관심 사항 구현 로직과 깔끔하게 유지할 수 있다.
	4.  변경이 필요하면 이 로직만 변경하면 된다. 
	5.  원하는 적용 대상을 선택할 수 있다.

- 아래 이미지에서, 프록시라는 단어가 나온다. 이게 뭘까?
	- 프록시(Proxy)란 **클라이언트와 웹 서버의 중간에서 어떠한 과정을 처리하는 구성**을 의미한다.
	- 클라이언트와 웹 서버의 사이에서 프록시를 진행하는 서버를 **프록시 서버**(Proxy Server)라고 한다.

- 프록시는 크게 두가지의 종류로 분류할 수 있다.
	- 1. **포워드 프록시**(Forward Proxy, 정방향 프록시)
	- 2. **리버스 프록시**(Reverse Proxy, 역방향 프록시)


> AOP 적용 전 의존관계 
![[AOP 적용 전 의존관계.png]]
- 기존에는, memberConroller에서 memberService를 바로 호출했다.
	- memberController가 memberService를 의존하는 관계

> AOP 적용 후 의존관계
![[AOP 적용 후 의존관계.png]]
- 만약, AOP가 있으면, 가짜 memberService를 만들어낸다. (여기서는, 이를 프록시라 함)
	- 이를, 프록시 방식의 AOP라고 한다.

- 스프링이 올라올 때, 스프링 컨테이너에 스프링 빈을 등록한다고 했다.
	- 이 때, AOP가 있으면 가짜 스프링 빈을 아래 이미지처럼 앞에 세워놓으며, 가짜 스프링 빈 내에서 `joinPoint.proceed()`가 실행되어야 진짜 스프링 빈이 실행된다.
	- 여기서 핵심은, memberConroller가 호출하는 것은 프록시라는 기술로 발생하는 가짜 memberService라는 것이다.

- 실제로, MemberController가 스프링 빈으로 등록되면서, 필요로 하는 MemberService가 호출될 때, 호출된 MemberSerive 인스턴스를 콘솔에 찍어보면 아래와 같다.
	- MemberService를 찾아서 복제하는 기술이며, 콘솔에 출력된 내용은 프록시이다.
```java
memberService is class hello.hellospring.service.MemberService$$SpringCGLIB$$0
```

> AOP 적용 전 전체 그림
![[AOP 적용 전 전체 그림.png]]

> AOP 적용 후 전체 그림
![[AOP 적용 후 전체 그림.png]]
- 만약, `@Around("execution(* hello.hellospring..*(..))")`와 같이 설정하면, `hello.hellospring` 경로 및 해당 경로 하위의 모든 클래스의 메소드에 대해 위와 같은 그림이 그려진다.
