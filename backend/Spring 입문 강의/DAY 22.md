
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

