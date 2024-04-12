
### 회원 관리 예제 - 웹 MVC 개발

- 회원 웹 기능 - 홈 화면 추가
- 회원 웹 기능 - 등록
- **회원 웹 기능 - 조회**


### 회원 웹 기능 - 조회 


> 회원 등록 폼 컨트롤러(`MemberController`)
```java
package hello.hellospring.controller;  
  
import hello.hellospring.service.MemberService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.Mapping;  
  

@Controller  
public class MemberController {  
    private final MemberService memberService;  
  
    @Autowired  
    public MemberController(MemberService memberService){  
  
        // 1. 스프링은 동작할 때 MemberController 객체를 생성한다.  
        // 2. 이 때, 이 생성자를 호출한다.  
        // 3. 생성자에 @Autowired이 있으면, memberService를 스프링이 스프링 컨테이너에 있는 memberService를 가져와서 연결시켜준다.  
        this.memberService = memberService;  
    }  
  
    @GetMapping("/members/new")  
    public String createForm(){  
        return "members/createMemberForm";  
    }  
}
```

> 회원 등록 폼 HTML (`src/resources/templates/members/createMemberForm.html)
```html
<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>Title</title>  
</head>  
<body>  
<div class="container">  
    <form action="/members/new" method="post">  
        <div class="form-group">  
            <label for="name">이름</label>  
            <input type="text" id="name" name="name" placeholder="이름을 입력하세요">  
        </div>  
        <button type="submit">등록</button>  
    </form>  
</div>  
</body>  
</html>
```
