
### 회원 관리 예제 - 웹 MVC 개발

- 회원 웹 기능 - 홈 화면 추가
- **회원 웹 기능 - 등록**
- 회원 웹 기능 - 조회


### 회원 웹 기능 - 등록 

##### 1. 회원 등록 폼(Form) 개발

- 먼저, `MemberController`에 Mapping 작업을 수행한 후, 연결되는 HTML파일을 만들어주자.
	- 이는 특정 경로의 Mapping을 검사하고, 회원 등록 폼을 사용자에게 보여주기 위한 목적으로 사용된다.

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

> 회원 등록 폼 HTML (`src/main/resources/templates/members/createMemberForm.html)
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

##### 2. 회원 등록 컨트롤러 

 >웹 등록 화면에서 데이터를 전달 받을 폼 객체 (말그대로 Form에서 입력된 값을 다룰 객체)
```java
package hello.hellospring.controller;  
  
  
public class MemberForm {  
private String name;  
  
    public String getName() {  
        return name;  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
}
```

> 회원 컨트롤러에서 회원을 실제 등록하는 기능 (추가: `create`메소드)
```java
package hello.hellospring.controller;  
  
import hello.hellospring.domain.Member;  
import hello.hellospring.service.MemberService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.Mapping;  
import org.springframework.web.bind.annotation.PostMapping;  
  
@Controller  
public class MemberController {  
  

    private final MemberService memberService;  
  
    @Autowired  
    public MemberController(MemberService memberService){  
        this.memberService = memberService;  
    }  
  
    @GetMapping("/members/new")  
    public String createForm(){  
        return "members/createMemberForm";  
    }  
  
    // /members/new 경로에서 발생한 POST 요청이 있는지를 검사하고, 있으면 아래의 create 메소드 실행 
    @PostMapping("/members/new")  
    public String create(MemberForm form){  
        Member member = new Member();  
        member.setName(form.getName());  
        memberService.join(member);  
          
        // 홈 화면으로 사용자 이동  
        return "redirect:/";  
    }  
  
}
```