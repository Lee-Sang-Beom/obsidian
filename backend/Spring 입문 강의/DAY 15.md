
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
	     
    @GetMapping("/members/new")  
    public String createForm(){  
        return "members/createMemberForm";  
    }
    
  	// 추가: members/createMemberForm
    @GetMapping("/members")  
    public String list(Model model){  
        List<Member> members = memberService.findMembers();  
        System.out.println(members);  
        model.addAttribute("members", members);  
        return "members/memberList";  
    }  
  
    @PostMapping("/members/new")  
    public String create(MemberForm form){  
        Member member = new Member();  
        member.setName(form.getName());  
        memberService.join(member);  
        return "redirect:/";  
    }  
  
}
```

> 회원 리스트 출력 HTML (`src/main/resources/templates/members/memberList.html)
```html
<!DOCTYPE html>  
  
<!--스키마 선언: 해당 선언문이 있어야 템플릿 엔진 구문 사용 가능-->  
<html xmlns:th="http://www.thymeleaf.org">  
<head>  
    <meta charset="UTF-8">  
    <title>Title</title>  
</head>  
<body>  
    <a href="/">돌아가기</a>  
    <div>  
        <table>  
            <thead>  
                <tr>  
                    <th>#</th>  
                    <th>이름</th>  
                </tr>  
            </thead>  
            <tbody>  
                <!--th:each는 Thymeleaf 템플릿 엔진에서 반복문을 처리하는 속성-->  
                <!--td태그에서 사용할 member라는 변수는 model에 넣은 members 리스트 각각의 요소이다.-->  
                <tr th:each="member : ${members}">  
                    <td th:text="${member.id}"></td>  
                    <td th:text="${member.name}"></td>  
                </tr>  
            </tbody>  
        </table>  
    </div>  
</body>  
</html>
```
