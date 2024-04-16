
## attribute

- HTML 문서의 **정적**인 속성을 뜻합니다. 
- attribute는 HTML 문서에서 element에 추가적인 정보를 넣을 때 사용되는 요소입니다.
    - 예를 들어, ```<div class = "container">```에서, div는 element(요소), class는 attribute(속성), container는 class attribute의 value(값)이 됩니다.

---

## property

- HTML 문서의 **동적**인 속성을 뜻합니다.
- property는 HTML 문서가 아니라, HTML 문서의 DOM 안에서 attribute를 가리키는 표현으로 말씀드릴 수 있습니다.

---

## 차이

이 두가지의 차이점은
 - attribute는 정적인 특성으로 변하지 않는 특성을 가지며
 - property는 자바스크립트의 DOM 조작으로, 동적으로 그 값이 변할 수 있다는 특성을 가진다는 점입니다.
    - 예를 들어 체크박스 태그가 있을 때 유저가 체크박스에 체크를 하면 **attribute의 상태는 변하지 않지만 property의 상태는 checked로 변하게 됩니다.**
