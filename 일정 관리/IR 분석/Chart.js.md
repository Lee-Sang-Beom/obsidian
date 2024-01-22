
#### import

- 해당 프로젝트는 Chart.js 라이브러리를 script로 불러오긴 하였으나 사용하지 못한 것으로 보이는데 그 이유는 아래와 같다.는데
	- 페이지 로드 후, 얻은 데이터로 차트를 그려야하는데 IIFE 특성을 고려한 코드가 작성되지 않았다.

- IIFE로 자바스크립트를 구성하는 해당 프로젝트는 반드시 차트를 포함해야 하는 페이지를 만들어야 한다면, IIFE 코드 최상단에 `htmlLoading`값을 true로 입력해야 한다.
 ```javascript
(function () {  
    return {  
        cssLoading: true,  
        htmlLoading: true,
	}
})
```