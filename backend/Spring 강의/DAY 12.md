
### 1. 자바 코드로 직접 스프링 빈 등록하기

- 컴포넌트 스캔 방법 말고, 직접 스프링 빈을 등록해보자.
	- 먼저, 회원 서비스와 회원 리포지토리의 `@Service, @Repository, @Autowired`등의 annotation을 지우고 시작해야 한다.

- 지웠다면, `hello.hellospring` 하위 `main`함수와 공통된 경로에 `SpringConfig.class` 파일을 만들어주자.
- 
