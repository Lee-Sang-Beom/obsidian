
#### 목적

- 페이지 관련 정보를 불러오거나 이동을 위해 사용하는 메소드
- 사용방법 및 예시는 아래와 같다.


#### PageManager.go()

- 단순히 페이지를 이동하기 위한 메소드이다.

```javascript
page.find('.resultSection').on('click',function (){  
    var indexPage = $(this).attr('indexPage')  
  
    if (indexPage == 1) {  
        PageManager.go(['teacher_achievement_evaluation/teacher_achievement_evaluation'],{'index': indexPage});  
    } else if (indexPage == 2) {  
        PageManager.go(['teacher_achievement_evaluation/teacher_achievement_evaluation'],{'index': indexPage});  
    } else if (indexPage == 3) {  
        PageManager.go(['teacher_achievement_evaluation/teacher_achievement_evaluation'],{'index': indexPage});  
    } else if (indexPage == 4) {  
        PageManager.go(['teacher_achievement_evaluation/teacher_achievement_evaluation'],{'index': indexPage});  
    }  
    page.tab.setTabIndex(indexPage, true);  
})
```