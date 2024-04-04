
#### 1. BOM(Browser Object Model)과 DOM(Document Object Model)

- 모든 서비스는 웹 브라우저를 바탕으로 실행되기 때문에, 웹 서비스 개발은 브라우저와 밀접한 관련이 있다.
	- 이 브라우저와 관련된 객체 집합이 **BOM(브라우저 객체 모델)** 이다. BOM을 이용해서, 브라우저와 관련된 기능을 구성할 수 있어요.

아래에서 다룰 **DOM은** BOM 중 하나이며, 이 BOM의 최상위객체는 **window**객체에요. window객체는 모든 객체가 소속된 객체이고, 전역 객체이면서 창이나 프레임을 의미해요. 아래와 같은 종류가 존재해요.

- window.screen
    
    - 사용자 환경의 디스플레이 객체
    
- window.location
    
    - 현재 페이지의 url을 다루는 객체
    
- window.navigator
    
    - 웹 브라우저 및 브라우저 환경 정보 객체
    
- window.history
    
    - 현재 브라우저가 접근해왔던 url history(기록)
    

참고로, 브라우저에서 제공하는 이 모든 기능을 통틀어 **Web API**라고 해요. Web API는 자바스크립트의 기능은 아니지만 자바스크립트 등에 의해 제어될 수 있도록 브라우저에서 제공하고 있어요.