### 스프링 부트 라이브러리 살펴보기

> **Gradle**(groovy 기반 오픈소스로 공개된 빌드도구)은 의존관계가 있는 라이브러리를 함께 다운로드 한다. 

#### 1. 스프링 부트 라이브러리
- spring-boot-starter-web  
	- spring-boot-starter-tomcat: **톰캣** (웹서버)  
	- spring-webmvc: **스프링 웹 MVC **

- spring-boot-starter-thymeleaf: **타임리프 템플릿 엔진**(View)  

- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅  
	- spring-boot  
    - spring-core  
- spring-boot-starter-logging  
    - logback, slf4j  



#### 2. 테스트 라이브러리**  

- spring-boot-starter-test  
	- **Junit**: **테스트 프레임워크** 
	- mockito: 목 라이브러리  
	- **assertj**: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리  
	- spring-test: 스프링 통합 테스트 지원