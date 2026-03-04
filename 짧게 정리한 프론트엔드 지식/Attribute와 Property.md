> 자바스크립트에서 `attribute`와 `property`는 DOM 요소와 상호작용할 때 중요한 개념이지만, 의미와 사용 방식에 차이가 있다.
#### 1. attribute(속성)

- HTML 문서의 **정적**인 속성을 뜻하며, **HTML 코드에 명시적으로 작성된 속성**을 의미한다.
	- 요소가 처음 렌더될 때 초기 상태를 정의한다.
	- 예를 들어 `<input type="text" value="Hello">`와 같이 HTML 요소에 속성으로 설정된 `value="Hello"`는 그 요소의 초기 상태를 나타낸다.

- 속성 값은 주로 **HTML에서 설정된 문자열 값**을 갖는다.
	- 속성은 자바스크립트에서 `getAttribute()`와 `setAttribute()` 메서드를 통해 읽거나 수정할 수 있습니다.

- attribute는 HTML 문서에서 element에 추가적인 정보를 넣을 때 사용되는 요소이다.
    - 예를 들어, ```<div class = "container">```에서, `div`는 element(요소), `class`는 attribute(속성), `container`는 `class attribute`의 value(값)이 된다.

```html
<input type="text" value="Hello"/>
```
```js
const input = document.querySelector("input");
console.log(input.getAttribute("value")); // "Hello"
input.setAttribute("value", "New Value"); // 속성 값을 변경
```

#### 2. property (프로퍼티)

- HTML 문서의 **동적**인 속성으로, 속성(attribute)이 HTML에서 정의된 초기 상태라면, 프로퍼티(property)는 JavaScript로 상호작용하는 동안 요소의 현재 상태를 나타낸다.

- 예를 들어, `<input type="text" value="Hello">`가 있다면, `value` 속성(attribute)은 HTML에서의 초기값 "Hello"이고, JavaScript로 접근할 수 있는 `input.value` 프로퍼티(property)는 그 시점의 입력된 값을 반영합니다.

- DOM 프로퍼티는 직접적으로 JavaScript 객체를 통해 접근할 수 있다.

```js
const input = document.querySelector("input");
console.log(input.value); // 현재 입력된 값
input.value = "Updated Value"; // 프로퍼티 값을 변경
```


#### 3. 차이

| 구분    | Attribute (속성)                      | Property (프로퍼티)                    |
| ----- | ----------------------------------- | ---------------------------------- |
| 역할    | HTML 문서에서 설정된 초기 상태                 | JavaScript를 통해 현재 상태로 반영됨          |
| 접근 방식 | `getAttribute()`, `setAttribute()`  | 직접 객체 프로퍼티 접근 (`element.property`) |
| 값     | 초기 문자열 값                            | 현재 요소의 실제 상태 값                     |
| 예시    | `<input value="Hello">`의 `value` 속성 | `input.value` 프로퍼티                 |
- 따라서, **속성**은 HTML에서 정의된 초기 상태이며, **프로퍼티**는 JavaScript에서 요소의 현재 상태를 반영하여 접근하고 조작하는 방식이다.
	- 예를 들어 체크박스 태그가 있을 때 유저가 체크박스에 체크를 하면 **속성**의 상태는 변하지 않지만 **프로퍼티**의 상태는 `checked`로 변하게 된다.

