
#### Triton
 - **페이지 초기화**`(onInit)`가 발생할 때나, 특정 이벤트성의 조작으로 **동적으로 페이지 구성**을 진행해야 할 때, 페이지 UI를 형성하기 위한 본 프로젝트 전용 라이브러리이다.
 
 - `new` 생성자로 호출 시, `page` 정보 및 `optional attr값`을 포함하는 Triton 컴포넌트들이 생성되며, `page`에 `append`되는 과정에서 구조화되며 **페이지의 UI가 형성**된다.


#### Triton 예시

```javascript
(function () {  
    return {       
	  resultView: true,
    },
})();
```



