
### 회원 관리 예제 - 웹 MVC 개발

- **회원 웹 기능 - 홈 화면 추가**
- 회원 웹 기능 - 등록
- 회원 웹 기능 - 조회


### 회원 웹 기능 - 홈 화면 추가

- 이제, MemberController를 통해 회원을 등록, 조회할 수 있는 작업을 할 수 있도록, 홈 화면을 추가하는 작업을 수행하자.

> **홈 컨트롤러(`HomeController.java`) 추가**
```java
package hello.hellospring.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
  
@Controller  
public class HomeController {  
  
    @GetMapping("/")  
    public String home(){  
        /**  
         * 1. URL 요청이 들어오고, 내장 Tomcat 서버가 최초로 요청을 받아 해당 요청을 스프링에게 넘긴다.  
         * 2. 스프링은 스프링 컨테이너 내부에, home.html과 맵핑된 컨트롤러가 있는지 먼저 찾아본다. (여기서는 HomeController에 GetMapping("/") 부분이 있음  
         * 3. 만약, 매핑된 컨트롤러가 있으면, 매핑된 컨트롤러 호출하고, 그 내부에서 return한 해당 html파일을 반환한다 (resources/templates/home.html)  
         * 4. 만약 매핑된 컨트롤러가 없으면, 스프링은 resources/static/home.html이 있는지를 확인한다.  
         */
        return "home";  
  
    }  
}
```

> **회원 관리용 홈 페이지 HTML 파일 (`src/main/resources/templates/home.html`) 추가**
```html
<!DOCTYPE html>  
<html xmlns:th="http://www.thymeleaf.org">  
  
<head>  
    <title>Hello</title>  
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />  
</head>  
  
<body>  
<div>  
    <div>  
        <h1>Hello Spring</h1>  
        <p>회원 기능</p>  
        <p>  
            <a href="/members/new">회원 가입</a>  
            <a href="/members">회원 목록</a>  
        </p>  
    </div>  
</div>  
</body>  
  
</html>
```