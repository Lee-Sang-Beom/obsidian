
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

- 근데 이걸, 모든 메소드에 대해 해야한다면?
	- 답이없다.


#### 3. AOP (Aspect Oriented Programming)

- AOP(Aspect-Oriented Programming)는 소프트웨어 공학에서 사용되는 프로그래밍 패러다임 중 하나이다.
	- AOP는 프로그램을 여러 관심사로 분리하여 개발하는 방법을 제공한다.

- 전통적인 객체지향 프로그래밍에서는 클래스와 객체를 중심으로 코드를 구성하며, 각 객체는 자신의 역할에 따라 코드를 구현한다.
	- 그러나 프로그램이 복잡해지면서 특정 관심사(로깅, 보안, 트랜잭션 처리 등)가 여러 객체에 걸쳐 중복되거나 분산되는 문제가 발생하기 시작했다.
	- 이로 인해 코드의 가독성과 유지보수성이 저하되는 문제가 생겼다.

- AOP는 이러한 문제를 해결하기 위해 등장했다. AOP는 관점(Aspect)을 기반으로 프로그램을 설계하며, 각 관점은 프로그램의 여러 부분에서 발생하는 특정 관심사를 나타낸다.
	- 이러한 관점은 프로그램의 핵심 로직과는 분리되어 관심사에 따라 모듈화된다.

- AOP에서는 관점을 **cross-cuttong concern(공통 관심 사항)**이라고 부른다. 이 횡단 관심사는 코드의 여러 지점에서 발생하며, 예를 들어 로깅, 보안, 트랜잭션 처리 등이 있을 수 있습니다.

AOP를 사용하면 횡단 관심사를 각 모듈에서 분리하여 중복을 줄이고, 코드의 재사용성과 유지보수성을 향상시킬 수 있습니다. AOP를 구현하기 위한 여러 기술과 프레임워크가 있으며, 대표적으로는 Spring Framework의 AOP 모듈이 있습니다. 이를 통해 메서드 실행 전후에 특정 동작을 추가하거나 예외를 처리하는 등의 작업을 할 수 있습니다.