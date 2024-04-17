
#### 1. JDBC 환경설정

- `build.gradle`파일에 JDBC, H2 데이터베이스 관련 라이브러리 추가
```gradle
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
runtimeOnly 'com.h2database:h2'
```

- `resource/application.properties`에 스프링 부트 
```null
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
```