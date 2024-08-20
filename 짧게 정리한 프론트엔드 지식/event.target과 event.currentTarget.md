
#### 1. `event.target`, `event.currentTarget`?

- `event.target`과 `event.currentTarget`는 자바스크립트와 타입스크립트에서 이벤트가 발생한 요소를 다룰 때 자주 사용되는 속성이다.
	- 이 두 속성은 비슷하지만 서로 다른 상황에서 사용되며, 그 차이는 다음과 같다:


#### 2. `event.target`

 - 이벤트가 발생한 실제 DOM 요소를 가리킨다.
	 - 예를 들어, 클릭한 버튼이나 텍스트 입력 필드 자체를 의미한다.

 - 사용자가 클릭하거나 입력한 요소와 같이 이벤트가 실제로 발생한 요소를 가리킨다.
	 - 이벤트가 버블링되는 경우, 이벤트 핸들러가 이벤트를 수신할 때에도 `event.target`은 여전히 최초로 이벤트가 발생한 요소를 가리킨다.

```html
<div id="parent">
  <button id="child">Click me</button>
</div>
<script>
  document.getElementById("parent").addEventListener("click", function(event) {
    console.log(event.target.id); // child
  });
</script>

```


#### 3. `event.currentTarget`

 - `event.currentTarget`은 `event.target`과 다르게, 이벤트 리스너를 가진 요소(이벤트 핸들러가 현재 처리되고 있는 요소)를 반환한다.
	 - 이벤트 핸들러가 연결(부착)된 요소를 가리킨다. 

- `event.currentTarget`은 이벤트가 실제로 발생한 요소가 아닐 수도 있다.
	- 예를 들어, 이벤트가 버블링되어 부모 요소에서 처리될 때, `event.currentTarget`은 이벤트 핸들러가 등록된 부모 요소를 참조한다.

```html
<div id="parent">
  <button id="child">Click me</button>
</div>
<script>
  document.getElementById("parent").addEventListener("click", function(event) {
    console.log(event.currentTarget.id); // parent
  });
</script>
```


#### 4. 주요 차이점 요약

- **`event.target`**: 이벤트가 **처음** 발생한 요소를 가리킴.
- **`event.currentTarget`**: 이벤트 핸들러가 **연결된** 요소를 가리킴.

이 차이점 때문에 이벤트가 버블링되거나 캡처링될 때 어떤 요소에 이벤트가 적용되었는지를 정확히 구분할 수 있다.