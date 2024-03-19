### View 환경설정 - Welcome Page 만들기

##### 1. 정적 페이지 만들어보기

- [참고자료: springboot의 index.html 설명](https://docs.spring.io/spring-boot/docs/current/reference/html/web.html#web)  

- 스프링 부트는 정적 및 템플릿 시작 페이지를 지원한다.  

- 먼저, 구성된 정적 콘텐츠 위치(static)에서 **index.html** 파일을 찾는다. 
	- 만약, 찾을 수 없으면 인덱스 템플릿(index.template)을 찾는다.  

- 둘 중 하나가 발견되면 자동으로 애플리케이션의 시작 페이지로 사용된다.  

- **resources/static/index.html**
```    
<html lang="en">  
<head>  
  <meta charset="UTF-8" />  
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />  
  <title>Document</title></head>  
<body>  
  Hello  <a href="/hello">hello</a></body>  
</html>  
```


##### 2. Thymeleaf 템플릿 엔진 사용해보기

- Thymeleaf 템플릿 엔진이라는 이름에서 **템플릿 엔진**이란, HTML을 정적으로 전달하는 것이 아니라, HTML을 서버에서 동적으로 바꿔서 전달하게끔 도와주는 도구를 의미한다.

- 아래는 관련 사이트이다.
	- [Thymeleaf 공식 사이트](https://www.thymeleaf.org/)  
	- [스프링 공식 튜토리얼](https://spring.io/guides/gs/serving-web-content/)  
	- [스프링부트 메뉴얼](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-template-engines )

- **src/main/java/hello/hellosptring/controller/HelloControll.java**
```java
package hello.hellospring.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
   
@Controller  
public class HelloController {  
    @GetMapping("hello")    
    public String hello(Model model){
        model.addAttribute("data","hello spring!");
        return "hello";  
    }
}  
```  

- **src/main/resources/templates/hello.html**
```html
<!DOCTYPE html>   
<html xmlns:th="http://www.thymeleaf.org">  
  <head>
      <title>Hello</title>
	  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  </head>  
  <body>  
    <p th:text="'thymeleaf 테스트 중입니다. ' + ${data}"></p>  
  </body>
</html>  
```  


##### 3. Thymeleaf 템플릿 엔진 동작과정 살펴보기

   - 먼저 웹 브라우저에서 `localhost:8080/hello` URL로 진입한다.

   - Spring Boot는 **Tomcat이라는 웹 서버**(정확히는 WAS)를 내장하고 있다.
	   - 이 내장 Tomcat서버가 경로 slot이 "/hello"임을 확인하고 spring한테 해당 경로에 대해 물어본다.  
   
   - Spring은 **HelloController**에서 @GetMapping("hello")부분을 통해, "/hello"부분에서 GET 요청이 이루어짐을 확인하고 매칭을 시켜준다. 
	   - 그 결과로 **public String hello 메소드**가 실행된다.
   
   - **public String hello 메소드**가 실행되면서, Spring은 argument로 model을 사용할 수 있도록, 모델을 만들어서 넘겨준다. 
	   - 그리고, hello메소드 내부에서는 넘겨받은 모델에 attributeName(key)와 attributeValue(value)라는 데이터를 추가한다.  
   
   - 마지막으로 public String hello 메소드는 return "hello"를 수행한다.
	   - 해당 return 문은 `resources/templates/hello.html` 파일을 연결시켜, 해당 파일을 렌더링 시켜 화면에 표시할 수 있도록 하는 구문이다. 
	   - 이 때, 화면을 찾아 처리하는 것은 `viewResolver`라는 것이 진행해준다.   
	
   - 그렇게 Controller에서 return 값으로 문자를 반환하면 viewResolver는 렌더링할 화면을 찾게되는데, 이 때, Spring Boot에서 아래와 같은 방법으로 viewName이 매칭된다.  
	   - `resources:templates/` + {ViewName} + `.html`
	   - 이 때, 매칭된 파일의 상단에서, `<html xmlns:th="http://www.thymeleaf.org">`와 같이 **스키마**가 선언되어 있어야 템플릿 엔진 구문을 사용할 수 있다.  
	   - `<body>`태그 내부에서, `<p th:text="'thymeleaf 테스트 중입니다. ' + ${data}"></p>`와 같은 방법으로 넘겨받은 모델의 attributeName(key) 이름을 사용해 value값을 웹 브라우저에 출력 할 수 있다.