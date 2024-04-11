### 1. 스프링 빈을 등록하고, 의존 관계 설정하기

- 지금까지, Member 객체를 만들고, MemberService와 MemoryMemberRepository 등을 만들었다.
	- 그리고 서비스를 통해 회원가입을 진행했고, 그 과정에서 리포지토리 내 데이터를 저장하거나 불러오는 과정도 수행했다.

- 기억이 안나면 다시보자
	- **`repository set`**: `store.put(member.getId(), member);`
	- **`repository get`**: `Optional.ofNullable(store.get(id))`


### 2. 작성 (1). MemberSevice

- `(main/java/hello.hellospring/service/MemberService.class)