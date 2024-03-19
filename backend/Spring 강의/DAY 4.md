### 1. MVC와 템플릿 엔진

- **MVC** : Model, View, Controller
- MVC와 템플릿 엔진 방식은 템플릿 엔진을 Model, View, Controller 방식으로 쪼개고, HTML을 좀 더 동적인 방식으로 프로그래밍한 후에 렌더링을 한 HTML 파일을 클라이언트에게 전달해주는 방식

### 2. Model

- 데이터베이스에서 데이터를 가지고 올 수 있고, 데이터를 가지고 있을 수 있다.
- 데이터베이스와 소통한다.
- 컨트롤러에게 데이터를 전달한다.
- 모델이 뷰와 직접 소통하는 일은 없다.
- 정리) Model은 소프트웨어나 애플리케이션에서 정보 및 데이터 부분을 의미한다. 이는 Controller에게 받은 데이터를 조작(가공)하는 역할을 수행한다고 볼 수 있다. 즉, 데이터와 관련된 부분을 담당하며 값과 기능을 가지는 객체라고 보면 된다.

### 3. View
- 유저가 보는 화면을 보여주게 하는 역할이다.
- 데이터를 받고 그리는 역할을 수행한다.
- 모델이나 데이터베이스와는 소통하지 않고 컨트롤러와만 소통한다.
- 컨트롤러에게 엑션이나 데이터를 전달만 하고 전달 받기만 한다.
- 정리) View는 입력값이나 체크박스 등과 같은 사용자 인터페이스 요소를 나타낸다. 이는 Controller에게 받은 Model의 데이터를 사용자에게 시각적으로 보여주기 위한 역할을 수행한다. 사용자에게 보여지는 화면이라고 볼 수 있다.

### 4. Controller
- 뷰에서 엑션과 이벤트에 대한 인풋 값을 받는다.
- 모델에게 전달해주기 전에 데이터를 가공할 수 있다.
- 뷰에게 모델에게 받은 데이터를 가공할 수 있다.
- 정리) Controller는 Model과 View 사이에서 데이터 흐름을 제어한다. 사용자가 접근한 URL에 따라 요청을 파악하고 URL에 적절한 Method를 호출하여 Service에서 비즈니스 로직을 처리한다. 이 후 결과를 Model에 저장하여 View에게 전달하는 역할을 수행한다. 결국 Controller는 Model과 View의 역할을 분리하는 중요한 요소이다.

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

    // 추가: MVC
    @GetMapping("hello-mvc")
    // 외부에서 파라미터를 받으려면, @RequestParams 사용
    public String helloMvc(@RequestParam("name") String name, Model model){
        // 파라미터로 넘어온 name을 model에 "name"이라는 key로 전달
        // ex) localhost:8080/hello-mvc?name="spring123"

        model.addAttribute("name", name);

        // resources/templates/[return String]와 연결
        return "hello-template";
    }
}

```

#### View

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  </head>

  <body>
    <p th:text="'hello-mvc' + ${name}">Empty..?</p>
  </body>
</html>
```

#### MVC, 템플릿 엔진 처리 과정 (강의자료 이미지 참고)

1. 웹 브라우저에서 `localhost:8080/hello-mvc` 요청을 한다.
2. 해당 요청은 내장 tomcat server에게 먼저 전달된다.
3. 내장 tomcat server는 `hello-mvc`라는 요청이 왔다고 spring에게 던진다.
4. spring은 **HelloController**에 매핑된 정보를 찾다가, `@GetMapping("hello-mvc")`와 매핑된 `public String helloMvc()`를 발견하고, 해당 메서드를 호출한다.
5. 해당 메서드는 `hello-template` 이름을 return하고, model에는 `(name: spring123)`에 대한 key-value 값을 전달한다. 이제 **viewResolver**라는 화면과 관련된 어떤 해결자가 동작(view를 찾아주고 템플릿 엔진과 연결시켜준다고 이해)한다.
6. viewResolver는 `templates/` 경로 하위에서 앞서 return한 String 값(hello-template)과 똑같은 html 파일을 찾아 thymeleaf 템플릿 엔진에게 처리해달라고 넘긴다
7. 템플릿 엔진은 해당 파일을 렌더링하고, 변환한 HTML을 웹 브라우저에게 반환한다. (정적일 때는 HTML을 그냥 반환했음)

#### 정리
1. 사용자의 Request(요청)를 Controller가 받는다.
2. Controller는 Service에서 비즈니스 로직을 처리한 후 결과를 Model에 담는다.
3. Model에 저장된 결과를 바탕으로 시각적 요소 출력을 담당하는 View를 제어하여 사용자에게 전달한다.