### 1. 스프링 빈을 등록하고, 의존 관계 설정하기

- 지금까지, Member 객체를 만들고, MemberService와 MemoryMemberRepository 등을 만들었다.
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

- 예제
### 2. 작성 (1). MemberSevice

- `(main/java/hello.hellospring/service/MemberService.class)