
#### 1. use strict

- 자바스크립트는 오류를 어느정도 무시하고 사용할 수 있었다.
	- 이는 편하게 코딩을 할 수 있도록 했지만, 때로는 심각한 버그를 만들도록 했다.
    - 이를 방지하기 위해 등장한 것이 **strict 모드**이다.

- `use strict`는 자바스크립트의 엄격 모드(strict mode)를 활성화하는 선언이다.
	- 이를 통해, 보다 엄격한 문법 및 실행 동작을 강제함으로써 코드의 오류를 줄이고 잠재적인 문제를 방지할 수 있다.
	- 엄격 모드는 ES5(ECMAScript 5)에서 도입되었다.

#### 2. 사용

- 사용 방법은 `"use strict"`를 파일 전체 및 함수 내에 적용하기 위해 아래처럼 선언하는 것이다.
	- 파일 전체
	- 함수 내
```ts
// 파일 전체에 엄격 모드 적용
"use strict";
function myFunction() {
    // 코드...
}

// 함수 내에서만 엄격 모드 적용
function myFunction() {
    "use strict";
    // 코드...
}
```

#### 3. 선언 효과

- 변수 선언 누락 방지
```js
"use strict";
x = 3.14; // ReferenceError: x is not defined
```

- 암시적 전역 객체 방지
```js
"use strict";
function myFunction() {
    this.x = 10; // TypeError: Cannot set property 'x' of undefined
}
```

- 중복 속성 이름 금지
```js
"use strict";
var obj = {
    prop: 1,
    prop: 2 // SyntaxError: Duplicate data property in object literal not allowed in strict mode
};
```

- with문 사용 금지
```js
"use strict";
with (obj) { // SyntaxError: Strict mode code may not include a with statement
    prop = 1;
}
```

- 예약어 사용 금지
```js
"use strict";
var let = 1; // SyntaxError: Unexpected strict mode reserved word
```