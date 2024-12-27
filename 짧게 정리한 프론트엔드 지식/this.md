
#### 1. this란?

- `this`는 함수가 호출될 때, 참조하는 객체를 나타내는 특별한 키워드이다.
	- `this`는 코드 어디서나 참조할 수 있지만, 대상이 상황에 따라 다르다.
	- 즉, `this`는 문맥에 의존하여 다양한 상황에서 다른 객체를 가리킬 수 있다는 것이다.


#### 2. 전역 스코프(global scope), 전역 문맥(global context)에서의 this

```js
console.log(this);  // 브라우저에서는 window 객체를 가리킴
```
- 전역 스코프에서 `this`는 `window/global`객체이다.
	- 전역에서 선언된 일반 함수 안에서의 `this`는 브라우저에서는 `window`, Node.js에서는 `global`객체이다.

```js
function highOrderFunction(callback) {
    callback(); // 일반 함수 호출로 실행됨
}

function callbackFunction() {
    console.log(this); // 전역 스코프에서 호출된 일반 함수
}

highOrderFunction(callbackFunction);
// 출력: window (브라우저 환경)
```
- 고차 함수가 콜백 함수를 **일반 함수처럼 호출하면**, `this`는 기본적으로 전역 객체 `window/global`를 참조한다.
	- 예제의 `callback`은 고차 함수의 **인자로 전달된 일반 함수**일 뿐이므로, 실행 시 `this`는 전역 객체를 참조한다.
	
```js
function highOrderFunction(callback) {
    callback();
}

const obj = {
    name: "example",
    method: function () {
        highOrderFunction(function () {
            console.log(this); // 일반 함수 호출
        });
    }
};

obj.method();
// 출력: window (브라우저 환경)
```
- 콜백 함수는 객체 `obj`의 메서드 안에서 전달되었지만, 호출 방식이 **일반 함수 호출**이므로 `this`는 전역 객체(`window`)를 참조한다.
	- 콜백 함수가 호출되는 순간, 호출한 주체(컨텍스트)가 명확하지 않으면 기본적으로 **전역 객체**를 참조하게 된다.
	- 여기서는, `callback()`은 단순히 `highOrderFunction` 내부에서 **전역적으로 호출**되므로, `this`는 전역 객체를 가리켰기 때문에 `this`가 전역 객체를 참조하게 된 것이다..

#### 3. 함수 내부

```js
function hello(){
	console.log(this);
}
hello();
```
- 위와 같은 일반 함수에서는 `use strict` 사용 여부에 따라 `this`가 다르게 출력될 수 있다.
	- `use strict` 사용(엄격 모드) : `undefined`
	- `use strict` 미사용(느슨한 모드) : `window`(`global this`)

#### 4. 메서드 호출 (Method Invocation)

```js
const obj = {
    name: 'John',
    greet: function() {
        console.log(this.name);
    }
};
obj.greet();  // 'John' 출력, this는 obj를 참조
```
- 객체의 메서드 내에서 `this`는 그 **객체 자체**를 참조한다.
	- 객체에 속한 메서드에서 `this`를 호출하면 해당 메서드를 포함하는 객체가 `this`이다.


#### 5. 생성자 함수 (Constructor Function)

```js
function Person(name) {
    this.name = name;
}
const person1 = new Person('Alice');
console.log(person1.name);  // 'Alice'
```
- 생성자 함수에서 `this`는 새로 생성되는 **인스턴스 객체**를 가리킨다.


#### 6. 화살표 함수 (Arrow Function)

```js
const obj = {
    name: 'John',
    greet: () => {
        console.log(this.name);
    }
};
obj.greet();  // undefined, 화살표 함수는 외부 문맥의 this(전역 객체)를 참조
```
- 화살표 함수에서는 `this`가 **자신의 문맥**을 따르지 않고, **외부 스코프의 `this`** 를 상속받는다. 그래서 일반 함수와 다르게 동작한다.


#### 7. 명시적 바인딩 (Explicit Binding)

```js
const obj = { name: 'Alice' };
function greet() {
    console.log(this.name);
}
greet.call(obj);  // 'Alice', call을 사용하여 this를 obj로 설정
```
- `call`, `apply`, `bind` 메서드를 사용하면 `this`를 명시적으로 설정할 수 있다.

> **바인딩**이란?
- 식별자와 값을 연결하는 **과정**을 의미한다.
- `this` 바인딩은 `this` 식별자 키워드와 `this`가 가리켜야 하는 객체를 연결하는 것을 의미한다.