
#### 1. 공통

```js
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
}

say('seoul'); // hello undefined, this is seoul
```

- 자바스크립트의 함수는 각자 `this`라는 것을 보유한다. 
	- `this`는 함수가 호출될 때 참조하는 객체를 나타내는 특별한 키워드이다.
		- `this`는 코드 어디서나 참조할 수 있지만, 대상이 상황에 따라 다르다.
		- 즉, `this`는 문맥에 의존하여 다양한 상황에서 다른 객체를 가리킬 수 있다는 것이다.

- 하지만, **상황에 따라 함수 내 this를 변경**해야 할 수 있다.
- `bind`, `apply`, `call` 메소드는 JavaScript에서 함수 호출 시 컨텍스트(즉, `this` 값)를 설정하거나 인자를 다루는 데 사용된다. 

---

#### 2. call

>  `originalFunction.call(thisArg, arg1, arg2, ...);`

```js
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
};

say.call(obj, 'seoul'); // hello tom, this is seoul

// ------------------

function foo(a, b, c) {
    console.log(a + b + c);
    console.log(this);
};


foo.call(obj, 1, 2, 3); // 6, { name: 'tom' }
```

- `call()` 메소드는, 변경할 `this`를 대체할 대상을 1번째 인수로 전송하면 된다. .
	- 그리고, 함수가 필요로 하는 파라미터가 있다면, **2번째 인수부터 쉼표로 구분하여 전달해주는 방식**을 사용한다.


#### 3. apply

> `originalFunction.apply(thisArg, [arg1, arg2, ...]);`

```js
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
};

say.apply(obj, ['seoul']) // hello tom, this is seoul

---

function foo(a, b, c) {
    console.log(a + b + c);
    console.log(this);
};
  
foo.apply(obj, [1, 2, 3]); // 6, { name: 'tom' }
```

- `apply()`메소드의 첫 번째 인수로는 `call()`와 똑같이 `this`를 대체할 대상을 전달한다. 
	- `apply()`는 `call()`와는 다르게, 두 번째 인수부터는 인수를 배열 형태로 전달하면 된다.


#### 4. bind

> `const boundFunction = originalFunction.bind(thisArg, arg1, arg2, ...);`

```js
const obj = {name: 'tom'};
const say = function(city){
  console.log(`hello ${this.name}, this is ${city}`);
};

const boundSay = say.bind(obj);
boundSay("busan"); // hello tom, this is busan
```

- `bind()` 메소드는 `call()`, `apply()`와 다르게 **새로운 함수를 생성**하고, 이 함수가 호출될 때 항상 특정한 `this` 값을 가지도록 한다.
	- 이 함수는 전달한 값을 `this`로 가지며, 함수를 가지고 있다가 나중에 함수를 사용할 수 있다.