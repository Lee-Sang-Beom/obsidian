
#### 1. 시작하기

- 먼저, [스프링 부트 이니셜라이저 페이지](https://start.spring.io/)로 이동하자.
- 그리고 아래 이미지와 같이 프로젝트를 세팅해주자.
![[스프링부트 이니셜라이저 선택화면.png]]

- 다음으로, 파일을 다운로드 받은 후 압축을 풀어 인텔리제이에서 해당 파일을 오픈하면 된다. 
![[최초 import 화면.png]]

- `build.gradle`의 dependencies를 확인해보면 이니셜라이저 페이지에서 추가한 의존파일 목록을 확인할 수 있다.
```gradle
dependencies {  
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'  
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'  
    implementation 'org.springframework.boot:spring-boot-starter-web'  
    compileOnly 'org.projectlombok:lombok'  
    runtimeOnly 'com.h2database:h2'  
    annotationProcessor 'org.projectlombok:lombok'  
    testImplementation 'org.springframework.boot:spring-boot-starter-test'  
}
```

- SDK가 설정되지 않았다고 뜨면, Java 17이상의 버전을 사용해주자.
	- 다음으로, `JpashopApplication.java` 파일을 실행시켜주면 아래와 같은 화면을 볼 수 있다. (실행 성공)
![[정상 설치 시, 초기 실행화면.png]]

