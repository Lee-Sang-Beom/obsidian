
#### 1. thymeleaf 템플릿 엔진

- **템플릿 엔진**이란, HTML을 정적으로 전달하는 것이 아니라, HTML을 서버에서 동적으로 바꿔서 전달하게끔 도와주는 도구를 의미한다.

- 스프링 부트 thymeleaf viewName 매핑방법은 아래와 같다.
	- `resources:templates/` + `{viewName}` + `.html`

- 아래는 관련 사이트이다.
	- [Thymeleaf 공식 사이트](https://www.thymeleaf.org/)  
	- [스프링 공식 튜토리얼](https://spring.io/guides/gs/serving-web-content/)  
	- [스프링부트 메뉴얼](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-template-engines )


#### 2. 동작 확인

> `/src/main/java/jpabook/jpashop/HelloController.java`
```java
package jpabook.jpashop;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
  
@Controller  
public class HelloController {  
  
    // HTTP GET 요청이 "/api/hello" 경로로 들어오는지 검사  
    @GetMapping("/api/hello")  
    public String Hello(Model model){  
        // Model 객체는 스프링 MVC에서 컨트롤러가 뷰에 데이터를 전달할 때 사용되는 일종의 컨테이너로, 이 객체를 사용하여 컨트롤러에서 생성한 데이터를 뷰로 전달할 수 있다.  
  
        // model.addAttribute("userName", "Lee")는 "userName"라는 이름의 속성에 "Lee"라는 값을 추가하는 역할을 한다.  
        // 이렇게 추가된 데이터는 해당 요청에 대한 뷰 템플릿에서 사용될 수 있다.  
        // 뷰 템플릿에서는 "userName"라는 이름으로 이 데이터를 참조할 수 있게 된다.  
        model.addAttribute("userName", "Lee");  
  
        // `resources:templates/` + `{viewName}` + `.html`  
        // 여기서는 `resources:templates/helloGetMapping.html`        return "helloGetMapping";  
    }  
}
```

> `/src/main/resources/templates/helloGetMapping.html`
```html
<!DOCTYPE HTML>  
<html xmlns:th="http://www.thymeleaf.org">  
<head>
	<title>Hello</title>  
    <meta content="text/html; charset=UTF-8" http-equiv="Content-Type"/>  
</head>  
<body>
	<p th:text="'안녕하세요. ' + ${userName}">안녕하세요. Guest</p>
</body>  
</html>
```


#### 3. 정적 페이지 하나 만들기

- 쉽게 말해, 클라이언트에게 파일을 그냥 그대로 내려주는 방법이다.
	- 변하지 않는(별도의 동적 동작이 없는) **html 파일**을 사용한다.

- 만약, [localhost:8080](localhost:8080/index.html)로 접근할 경우, 정적 컨텐츠 처리 방식은 아래와 같다.

```
1. 웹 브라우저가 localhost:8080로 접근한다.
2. 내장 Tomcat 서버가 최초로 요청을 받고, 해당 요청을 스프링에게 넘긴다.
3. 스프링 컨테이너 내부에, index.html과 맵핑된 컨트롤러가 있는지 먼저 찾아본다.
4. 만약 없으면, 스프링 "resources/static/index.html"이 있는지 찾아본다.
5. 만약 여기 있으면, 이 html 파일을 반환한다. 이것이 정적 컨텐츠의 동작 방식이다.
```

> `src/main/resources/static/index.html`
```html
<!DOCTYPE HTML>  
<html xmlns:th="http://www.thymeleaf.org">  
<head>
	<title>Hello</title>  
    <meta content="text/html; charset=UTF-8" http-equiv="Content-Type"/>  
</head>  
<body> Hello <a href="/api/hello">이동</a></body>  
</html>
```