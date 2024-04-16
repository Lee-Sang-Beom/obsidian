
## event.target 과 event.currentTarget의 차이점은?

 - 이 둘의 차이는 어떤 것을 반환하느냐의 차이로 구분할 수 있습니다.


### event.target
 - **event.target**은 이벤트가 발생한 요소를 가리킵니다.

- 즉 사용자가 클릭한 그 자체의 요소를 반환합니다. 

### event.currentTarget
 - **event.currentTarget**은, 이벤트 리스너를 가진 요소를 반환합니다.
 
 - 즉, 이벤트(**예시 : onClick**)를 실행할 수 있도록 하는 요소를 의미합니다.