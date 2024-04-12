
### 회원 관리 예제 - 웹 MVC 개발

- 회원 웹 기능 - 홈 화면 추가
- 회원 웹 기능 - 등록
- **회원 웹 기능 - 조회**


### 회원 웹 기능 - 조회 


> 회원 컨트롤러 내 조회기능(`MemberController`)
```java
package hello.hellospring.controller;  
  
import hello.hellospring.domain.Member;  
import hello.hellospring.service.MemberService;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.Mapping;  
import org.springframework.web.bind.annotation.PostMapping;  
  
import java.util.List;  
  
@Controller  
public class MemberController {  
  
    private final MemberService memberService;  
  
  
    @Autowired  
    public MemberController(MemberService memberService){  
        this.memberService = memberService;  
    } 
	     
	// 추가: members/createMembe
    @GetMapping("/members/new")  
    public String createForm(){  
        return "members/createMemberForm";  
    }  
  
    @GetMapping("/members")  
    public String list(Model model){  
        List<Member> members = memberService.findMembers();  
        System.out.println(members);  
        model.addAttribute("members", members);  
        return "members/memberList";  
    }  
  
    // /members/new 경로에서 발생한 POST 요청이 있는지를 검사하고, 있으면 아래의 create 메소드 실행  
    @PostMapping("/members/new")  
    public String create(MemberForm form){  
        Member member = new Member();  
        member.setName(form.getName());  
        memberService.join(member);  
  
        // 홈 화면으로 사용자 이동  
        // (example: /blog로 이동) -> redirect:/blog  
        return "redirect:/";  
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
