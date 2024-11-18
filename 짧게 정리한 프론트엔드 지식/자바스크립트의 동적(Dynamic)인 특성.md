#### 1. 웹 페이지에서 동적 언어의 의미

- 웹 페이지에서, 정적 언어는 말그대로 움직이지 않는다는 뜻을 가진다. 
	- HTML과 CSS로만 작성된 페이지는, 작성한 코드 그대로만 페이지를 보여줄 뿐, **상호작용을 발생시킬 수 없다**는 뜻이다.

- 하지만, 웹 페이지에서의 동적 언어는 사용자가 발생시키는 이벤트 정보를 토대로 웹페이지에 상호작용을 가할 수 있도록 하는 언어이다. 
	- 동적언어를 이용하면 코드에 따라 사용자가 어떤 행동을 했을 때 경고문이 보여진다거나, 버튼을 눌렀을 때 웹페이지 내용이 변경되게끔 하는 등의 웹페이지 변화를 유도할 수 있다.


#### 2. 자바스크립트의 동적 특성

- 자바스크립트는 컴파일 시간에 변수의 타입이 결정되는 것이 아니라, 런타임에 타입이 결정되는 언어이다.
	- 즉, **자료형**이 소스가 빌드될 때 결정되는 게 아니라 실행 시 결정된다는 의미이기 때문에, C,C++,Java처럼 변수에 들어갈 값 형태에 따라 자료형을 미리 지정해줄 필요가 없다.
	- 하지만, 컴파일 시간에 변수 타입을 체크해 실행 중에 발생 가능한 버그들을 미리 확인할 수 있는 **정적 언어**와 달리, 자바스크립트는 실행 도중에 변수에 예상치 못한 타입이 들어와 `Type Error`를 발생시킬 수 있다.

- 자바스크립트는 동적 프로그래밍 언어로 분류되며, 다음과 같은 특성을 지닌다.

> **동적 타입** (Dynamic Typing)
```js
let x = 42;      // 숫자
x = "Hello";     // 문자열로 변경
x = true;        // 불리언으로 변경
```

> **동적 객체** (dynamic objects)
```js
let obj = { name: "John" };
obj.age = 30;       // 동적으로 프로퍼티 추가
delete obj.name;    // 동적으로 프로퍼티 삭제
```

> **일급 객체**
> - 함수는 변수에 할당하거나, 다른 함수의 인자로 전달하거나, 반환값으로 사용할 수 있다.
> - 런타임에 함수 동작을 동적으로 정의하거나 호출할 수 있다.
```js
function greet(name) {
  return `Hello, ${name}`;
}
let sayHi = greet;
console.log(sayHi("World")); // Hello, World
```


#### 3. 동적 동작을 가능케하는 요소들

> **`eval()` 메소드**
- 문자열을 코드로 평가하여 실행한다. 매우 동적이지만 보안과 성능 문제로 사용이 권장되지 않는다.
```js
eval("let a = 10; console.log(a);"); // 10
```

>**`this` 키워드**
- 런타임에 컨텍스트(context)에 따라 값이 동적으로 바뀐다.
```js
const obj = {
  name: "Alice",
  getName: function () {
    return this.name;
  },
};
console.log(obj.getName()); // Alice

const getName = obj.getName;
console.log(getName()); // undefined (전역 컨텍스트)
```

> **프로토타입(prototype)**
- 객체의 프로토타입 체인으로 런타임에 상속 동작을 변경할 수 있다. 
```js
function Person(name) {
  this.name = name;
}
Person.prototype.sayHello = function () {
  return `Hello, ${this.name}`;
};
let john = new Person("John");
console.log(john.sayHello()); // Hello, John
```


#### 4. 자바스크립트 동적 기능의 장단점

> **장점**
1. **유연성**: 코드 작성이 간단하며 다양한 문제를 빠르게 해결 가능
2. **빠른 프로토타이핑**: 동적 언어 특성으로 개발 초기 단계에서 유용
3. **확장성**: 객체, 함수 등을 런타임 중에 변경하거나 확장 가능

> **단점**
1. **런타임 에러 가능성**: 컴파일 타임에 타입 검사가 없기 때문에 에러가 런타임에 발생
2. **가독성 저하**: 동적 타입 변경으로 인해 코드 추적이 어려울 수 있음
3. **성능 문제**: 동적 타입 처리와 런타임 평가로 인해 정적 언어에 비해 성능이 떨어질 수 있음


#### 5. 동적 언어(Dynamic Language)로서의 자바스크립트

- 동적 언어는 컴파일 타임이 아니라 런타임에 많은 동작을 처리한다.
- 자바스크립트는 대표적인 동적 언어로 다음 기능을 제공한다.
    - **동적 메소드 호출**: 함수 이름을 문자열로 호출.
    - **동적 속성 접근**: 대괄호 표기법으로 속성 동적 접근.
```js
let methodName = "sayHello";
let obj = {
  sayHello: function () {
    return "Hello!";
  },
};
console.log(obj[methodName]()); // Hello!
```


#### 6. 동적 프로그래밍과 활용 사례

- **프론트엔드 개발**
	- DOM 조작: HTML 요소를 런타임에 동적으로 생성하거나 삭제.
	- 이벤트 핸들러: 동적으로 이벤트를 할당.
```js
let button = document.createElement("button");
button.innerText = "Click me";
button.onclick = () => alert("Button clicked!");
document.body.appendChild(button);
```

- **백엔드 개발**
	- 동적 라우팅: URL에 따라 동적으로 API 동작 정의.
	- JSON 처리: 동적 데이터 구조를 JSON으로 관리.