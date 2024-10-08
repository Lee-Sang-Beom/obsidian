
#### 1. 고차함수

- 자바스크립트에서의 함수는 **일급 객체**이다.
	- 때문에, 함수를 값처럼 인자로 전달할 수 있으며, 반환할 수도 있다.

```js
// 고차 함수 예시: 함수를 인자로 받는 함수
function repeatOperation(operation, times) {
    for (let i = 0; i < times; i++) {
        operation();
    }
}

function sayHi() {
    console.log("Hi there!");
}

// sayHi 함수를 인자로 전달
repeatOperation(sayHi, 3);
// 출력: 
// Hi there!
// Hi there!
// Hi there!

// 고차 함수 예시: 함수를 반환하는 함수 (클로저)
function createGreeting(greeting) {
    return function(name) {
        return `${greeting}, ${name}!`;
    };
}

const sayHello = createGreeting("Hello");
const sayGoodbye = createGreeting("Goodbye");

console.log(sayHello("Alice"));  // "Hello, Alice!"
console.log(sayGoodbye("Bob"));  // "Goodbye, Bob!"
```
- 그래서 자바스크립트에는 **고차 함수**라는 용어가 있다.  
	- **고차함수**는 변수 뿐 아니라, **함수**를 인자로 받거나 또는 함수를 출력으로 반환하는 방식으로 작동하는 함수를 의미한다.
	- 자바스크립트에서 고차 함수는 굉장히 광범위하게 사용되고 있다. 어느정도나면, 고차함수가 무엇인지 몰라도, 고차함수를 이용하여, 프로그래밍을 해왔을 정도이다.
	- 관련 메소드로, `map, filter, sort` 등이 있다.
	
- 고차함수는 인자로 받은 함수를 필요한 시점에 호출하거나 **클로저**를 생성하는 방법으로 반환할 수 있다.
	- 클로저는 나중에 다루겠지만, 간단히 요약해보자면 **특정 함수 내에서 반환된 내부 함수가, 스택에서 제거된 당시의 환경을 기억하여 접근할 수 있도록 하는 반환된 내부함수와 내부함수가 선언되었던 렉시컬 환경의 조합이다**.


#### 2. 일급객체란?

- 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체를 일급객체라고 한다.

```js
// 함수를 변수에 할당
const greet = function(name) {
    return `Hello, ${name}!`;
};

console.log(greet("John")); // "Hello, John!"

// 함수를 다른 함수의 인자로 전달 (콜백)
function sayHello(callback, name) {
    console.log(callback(name));
}

sayHello(greet, "Alice"); // "Hello, Alice!"

// 함수를 다른 함수에서 반환
function createMultiplier(multiplier) {
    return function(num) {
        return num * multiplier;
    };
}

const double = createMultiplier(2);
console.log(double(5)); // 10
```

- **일급객체**이기 위한 조건은 아래와 같다.
    - 변수에 할당(assignment)할 수 있어야 한다.
    - 다른 함수에 인자(argument)로 전달할 수 있다.
    - 다른 함수의 결과로서 return될 수 있다.
    
- 자바스크립트에서, 함수는 **일급객체**이다. 때문에, **고차 함수**를 만들 수 있고, **콜백(callback)** 을 사용할 수 있다.
	- 함수를 변수에 할당할 수 있고, 인자로 전달할 수 있고, return될 수도 있기 때문이다.
	- 이게 위에서 보았었던 고차함수(Higher order function)의 개념이다.
	- 참고로 **콜백(callback)** 이란, **전달 인자(argument)** 로 전달받은 함수를 의미한다.
		- 매개변수(parameter): 함수 안에서의 정의 및 사용에 나열되어 있는 변수로, **함수를 정의할 때 사용되는 변수**이다.
		- 전달 인자(argument): **실제로 함수가 호출될 때, 넘겨주는 변수값**이다.