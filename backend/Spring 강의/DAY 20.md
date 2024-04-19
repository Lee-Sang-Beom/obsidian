
#### 1. JPA의 정의

- JPA(Java Persistence API)는, Java 언어를 사용하여 관계형 데이터베이스를 다루는 데 사용되는 자바 표준 ORM(Object-Relational Mapping) 기술이다.
	- ORM은 객체 지향 프로그래밍 언어에서, 객체와 관계형 데이터베이스의 테이블 간의 매핑을 자동화하는 기술을 의미한다. 
	- 개발자는 객체 지향적인 방식으로 데이터를 다루고, ORM 프레임워크가 이를 관계형 데이터베이스에 맞게 변환하여 데이터베이스와의 상호작용을 수행할 수 있다.

- JPA는 이러한 ORM 기술을 자바에서 사용할 수 있도록 표준화한 API이다.
	- JPA를 사용하면 **개발자는 데이터베이스와의 상호작용을 위해 SQL 쿼리를 직접 작성하는 대신, 객체를 사용하여 데이터를 조회, 저장, 수정 및 삭제**할 수 있다.
	- 즉, SQL과 데이터 중심 설계에서, 객체 중심의 설계로 패러다임을 전환할 수 있도록 한다.
	- 이는 개발 생산성을 향상시키고, 코드의 가독성을 높이며, 데이터베이스에 대한 의존성을 줄이는 데 도움이 된다.

- JPA는 주로 Java EE(Enterprise Edition) 및 Java SE(Standard Edition) 애플리케이션에서 사용되며, 대표적인 JPA 구현체로는 Hibernate, EclipseLink, OpenJPA 등이 있다.
	- 이러한 JPA 구현체들은 JPA 표준을 구현하면서 각각의 특징과 성능을 제공한다.


#### 2. JPA 환경 설정하기

- `build.gradle` 파일에 JPA 관련 라이브러리 추가
```gradle
dependencies {  
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'  
    implementation 'org.springframework.boot:spring-boot-starter-web'  
    // implementation 'org.springframework.boot:spring-boot-starter-jdbc'  
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'  
    runtimeOnly 'com.h2database:h2'  
    testImplementation 'org.springframework.boot:spring-boot-starter-test'  
}
```

- 스프링 부트(`resources/application.properties`)에 JPA 설정 추가
```null
spring.datasource.url=jdbc:h2:tcp://localhost/~/test  
spring.datasource.driver-class-name=org.h2.Driver  
spring.datasource.username=sa  

# 추가
spring.jpa.show-sql=true  
spring.jpa.hibernate.ddl-auto=none
```