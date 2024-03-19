
### 1. 빌드하여 실행하기 `(window - cd)`

1. 명령 프롬프트 이동 후 스프링 프로젝트까지 cd이동
2. 빌드 명령어인 `gradlew build` 입력
3. cd build/libs로 빌드한 폴더 내부로 이동
4. `java -jar [생성된 .jar 파일명]` 입력
5. 8080포트에 서버가 실행된다.

> Tip: 스프링 프로젝트까지 cd이동 후, `gradlew clean` 명령어 입력 시 빌드폴더가 사라짐

### 2. 정적 컨텐츠

- 쉽게 말해, 클라이언트에게 파일을 그냥 그대로 내려주는 방법이다.
- 변하지 않는(별도의 동적 동작이 없는) html파일을 사용한다.

> 만약, [localhost:8080/testStatic.html](localhost:8080/testStatic.html)로 접근할 경우, 정적 컨텐츠 처리 방식은 아래와 같다.

```
1. 웹 브라우저가 localhost:8080/testStatic.html을 요청한다.
2. 내장 Tomcat 서버가 최초로 요청을 받고, 해당 요청을 스프링에게 넘긴다.
3. 스프링 컨테이너 내부에, testStatic.html과 맵핑된 컨트롤러가 있는지 먼저 찾아본다.
4. 만약 없으면, 스프링 부트는 "resources/static/testStatic.html"이 있는지 찾아본다.
5. 만약 여기 있으면, 이 html 파일을 반환한다. 이것이 정적 컨텐츠의 동작 방식이다.
```
