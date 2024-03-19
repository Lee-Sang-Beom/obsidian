
### 1. API

- API 방식은 `@ResponseBody` 문자를 반환하는 방법으로 사용할 수 있다.
- `@ResponseBody`를 사용하면, ViewResolver를 사용하지 않는데, 이는 HTTP의 BODY에 문자 내용을 직접 반환한다는 뜻이다.
- JSON 형식의 `{key: value , ...}` 형태의 값이 반환된다.

#### Controller

```java
package hello.hellospring.controller;

import org.springframework.boot.SpringApplication;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import hello.hellospring.HelloSpringApplication;

@Controller
public class HelloController {
    // class 추가
    static class Hello {
        private String name;

        public String getName(){
            return name;
        }

        public void setName(String name){
            this.name = name;
        }
    }

    public static void main(String[] args) {
		SpringApplication.run(HelloSpringApplication.class, args);
	}

    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute("data","hello spring!");
        return "hello";
    }

    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model){
        model.addAttribute("name", name);
        return "hello-template";
    }

    // 추가
    @GetMapping("hello-api")
    @ResponseBody
    public Hello HelloApi(@RequestParam("name") String name) {
        Hello helloObj = new Hello();
        helloObj.setName(name);
        return helloObj;
    }
}

```

#### API 처리 과정 (강의자료 이미지 참고)

1. 웹 브라우저에서 `localhost:8080/hello-api` 요청을 한다.
2. 해당 요청은 내장 tomcat server에게 먼저 전달된다.
3. 내장 tomcat server는 `hello-api`라는 요청이 왔다고 spring에게 던진다.
4. spring은 **HelloController**에 매핑된 정보를 찾다가, `@GetMapping("hello-api")`와 매핑된 `public Hello HelloApi()`를 발견하고, 해당 메서드를 호출한다.
5. 기존, 템플릿엔진은 요청에 맞는 템플릿 정보가 담긴 HTML 파일을 찾기위해 viewResolver에게 요청을 전달했다. 하지만, `@ResponseBody`가 붙어있는 경우 다르게 동작한다.(default 방식인 기본정책으로, 객체의 경우 JSON방식으로 데이터를 만들어 HTTP 응답 Body에 데이터를 담아 전달함)

   - 일단, ViewResolver가 동작하는게 아니라, *HTTPMessageConverter*가 동작한다. 만약, 단순 문자열을 반환해야 하는 경우라면 `StringConverter`라는게 동작하고, 객체를 반환해야 한다면 `JsonConverter`라는 것이 동작(JSON 스타일로 객체를 변환)한다.
   - byte 처리 등등 기타 여러 **HTTPMessageConverter**가 기본으로 등록되어 있다.
   - spring은 클라이언트의 HTTP Accept 헤더와 서버에서 작성된 Controller 반환 타입 정보를 알아서 조합해서 **HTTPMessageConverter**가 선택된다.

6. 웹 브라우저에게 반환하는 데이터가 객체 타입이든, string 타입이든 HTTP 응답 헤더의 Body에 데이터를 넣어 반환한다.

![[Pasted image 20240319093438.png]]