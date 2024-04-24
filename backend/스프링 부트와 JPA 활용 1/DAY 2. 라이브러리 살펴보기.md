
- 먼저, 설치된 라이브러리는 아래와 같다.
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

- 그리고, 이와 관련된 Dependencies는 우측 Gradle에서 확인할 수 있다.
![[gradleDependencies.png]]

