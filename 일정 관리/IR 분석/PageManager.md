```javascript
//그래프 링크 이동  
item.find('#field_practice1').on('click',function () {  
    PageManager.go(['education/field_practice']);  
}).css('cursor', 'pointer')  
item.find('#capstone_design1').on('click',function () {  
    PageManager.go(['education/capstone_design']);  
}).css('cursor', 'pointer')  
item.find('#entrepreneurship1').on('click',function () {  
    PageManager.go(['education/entrepreneurship']);  
}).css('cursor', 'pointer')
```