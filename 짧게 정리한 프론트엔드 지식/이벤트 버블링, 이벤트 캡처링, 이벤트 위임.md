
#### 1. **이벤트 버블링 (Event Bubbling)**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Event Bubbling Example</title>
</head>
<body>
    <div id="parent" style="padding: 20px; background-color: lightblue;">
        Parent Div
        <button id="child" style="margin-top: 10px;">Click Me</button>
    </div>

    <script>
        // 부모 요소에 이벤트 리스너 추가
        document.getElementById("parent").addEventListener("click", () => {
            alert("Parent Div Clicked (Event Bubbling)");
        });

        // 자식 요소에 이벤트 리스너 추가
        document.getElementById("child").addEventListener("click", (event) => {
            alert("Button Clicked");
            // 이벤트 버블링을 멈추려면 아래 주석 해제
            // event.stopPropagation();
        });
    </script>
</body>
</html>
```
- **정의**:
	- 이벤트 버블링은 특정 요소에서 발생한 이벤트가 그 요소의 부모 요소로 전달되고, 그 부모 요소의 부모로 계속 전달되는 과정을 말한다.
	- 마치 물방울이 아래에서 위로 올라가는 것처럼, 이벤트가 가장 깊은 요소에서 시작해 위로 전파된다.

- **예시:**
	- 만약 버튼을 클릭했다면, 그 클릭 이벤트는 버튼 → 버튼의 부모 → 그 부모의 부모 → ... → `<body>` → `<html>`까지 전파된다.
	- 이 때, 모든 부모 요소는 이 이벤트를 처리할 기회를 갖는다.

- 만약, 원하는 타겟 요소에서만 이벤트 핸들러가 동작하게 하고 싶다면, 이벤트 객체의 메소드인`event.stopPropagation()` 를 사용할 수 있다.
    - 이 함수는, 핸들러에게 이벤트를 처리하도록 한 후 버블링을 중단하도록 명령한다.

- 위 예제코드에서는, `"id=child"` 버튼을 클릭하면 먼저 버튼의 이벤트가 실행된 후 부모 `div`의 이벤트가 실행된다. 
	- `stopPropagation()`을 사용하면 버블링을 막을 수 있다.


#### 2. **이벤트 캡처링 (Event Capturing)**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Event Capturing Example</title>
</head>
<body>
    <div id="parent" style="padding: 20px; background-color: lightcoral;">
        Parent Div
        <button id="child" style="margin-top: 10px;">Click Me</button>
    </div>

    <script>
        // 부모 요소에 캡처링 이벤트 리스너 추가
        document.getElementById("parent").addEventListener("click", () => {
            alert("Parent Div Clicked (Event Capturing)");
        }, { capture: true });

        // 자식 요소에 이벤트 리스너 추가
        document.getElementById("child").addEventListener("click", () => {
            alert("Button Clicked");
        });
    </script>
</body>
</html>
```

- **정의:**
	- 이벤트 캡처링은 이벤트 버블링의 반대 방향으로, 최상위 부모 요소에서 시작해 자식 요소로 내려가며 이벤트를 처리하는 과정이다. 
	- 이벤트가 가장 바깥의 요소에서부터 시작해, 점점 안쪽으로 들어가며 해당 이벤트를 처리한다.

- **예시:**
	- `document` → `<html>` → `<body>` → ... → 클릭한 요소 순서로 이벤트가 전달된다.

- 캡쳐링을 수행하기 위해서는 **이벤트 핸들러에 {capture: true} option을 적용해** 따로 캡쳐링 옵션을 true로 해주어야 한다.

- 위 예제에서는, `{ capture: true }` 옵션을 사용하여 캡처링을 활성화했다. 
	- 이 경우 클릭 이벤트가 부모부터 시작해 자식 요소로 내려간다.


#### 3. **이벤트 위임 (Event Delegation)**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Event Delegation Example</title>
</head>
<body>
    <ul id="itemList">
        <li data-action="edit">Edit Item</li>
        <li data-action="delete">Delete Item</li>
        <li data-action="view">View Item</li>
    </ul>

    <script>
        // 이벤트 위임: 리스트의 부모 요소에 이벤트 리스너 추가
        document.getElementById("itemList").addEventListener("click", (event) => {
            const action = event.target.getAttribute("data-action");
            
            if (action) { // 클릭한 요소에 data-action 속성이 있을 때
                switch(action) {
                    case "edit":
                        alert("Editing item...");
                        break;
                    case "delete":
                        alert("Deleting item...");
                        break;
                    case "view":
                        alert("Viewing item...");
                        break;
                }
            }
        });
    </script>
</body>
</html>
```

- **정의:**
	- 이벤트 위임은 공통된 부모 요소에 이벤트 리스너를 설정하여 여러 자식 요소들의 이벤트를 한 번에 처리하는 방법이다. 
	- 즉, 자식 요소에 각각 이벤트 리스너를 붙이지 않고, 부모 요소에 하나의 이벤트 리스너만 붙여도 모든 자식 요소의 이벤트를 관리할 수 있다.

- **예시:**
	- 정해진 액션에 따라서 다른 동작을 하는 여러 버튼에 대한 이벤트에 대해, 각 버튼에 대해서 이벤트 리스너를 등록하면 비효율적인 상황이 되는 상황이 있다고 가정하자. 
		- 이 때, **이벤트 위임 방식**을 이용해 공통 부모에 이벤트를 등록해주고, 정해진 `data-action`에 따라 다른 함수를 실행하는 방법을 사용할 수 있다.
	
	- 목록의 항목을 클릭할 때, 각 항목에 리스너를 다는 대신, 목록 전체에 리스너를 달아둔다. 
	- 클릭된 항목이 무엇인지 이벤트 객체를 통해 확인하고, 그에 따라 동작을 실행한다.

- 또한 동적으로 추가되고 삭제되는 엘리먼트에 대해서도, 부모 요소에 이벤트 위임방식을 적용해서 이벤트를 효율적으로 관리할 수도 있다.

- 위 예제에서는, `ul` 요소에 이벤트 리스너를 추가하여 `li` 항목에 대한 클릭을 처리한다. 
	- 이렇게 하면 동적으로 추가된 항목도 `ul`의 이벤트 리스너로 관리할 수 있어 효율적이다.


#### 4. **요약**

- **이벤트 버블링:** 자식 → 부모 방향으로 이벤트 전파.
- **이벤트 캡처링:** 부모 → 자식 방향으로 이벤트 전파.
- **이벤트 위임:** 부모 요소에서 자식 요소들의 이벤트를 한 번에 처리. 효율적이고 관리하기 쉬운 방법.
	- 이벤트 위임은 특히 동적으로 추가되는 요소를 처리할 때 유용하다.