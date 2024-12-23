
#### 1. 제너레이터(Generator)

- **제너레이터(Generator)** 는 함수 실행을 일시 중지하고 다시 시작할 수 있는 특별한 함수이다.
- 일반 함수와 달리, 제너레이터는 **`function*` 키워드**로 정의되며, 호출 시 즉시 실행되지 않고 **이터레이터(Iterator)** 객체를 반환한다.
	- 이를 통해 값을 순차적으로 생성하거나 제어할 수 있는데, 제너레이터는 특히 비동기 처리나 큰 데이터 집합을 단계별로 처리할 때 유용하다.


#### 2. iterable, iterator

- 제너레이터는 **iterable하고 iterator**한 특징을 가진다.
	- iterable: JavaScript에서 `Iterable`은 반복 가능한 객체를 의미하며, `for...of` 루프나 전개 연산자(`...`) 등과 같은 반복문에서 사용할 수 있는 객체이다.
		- 반복 가능한 객체는 `Symbol.iterator()` 메소드를 구현하고 있어야 하며, 이 메소드는 객체가 어떻게 반복될지 정의한다.
	
	- iterator: 반복 작업을 제어하는 객체로, 반복되는 시퀀스의 값을 하나씩 반환할 수 있다.
	    - `next()` 메서드를 호출할 때마다 순차적으로 값을 반환하며, 반복이 끝나면 `done: true`가 반환된다.


#### 3. 제너레이터의 특징

- 제너레이터 함수를 실행하면, **iterator**한 제너레이터 객체가 반환된다.
	- 반환된 iterator객체가 갖고있는 `next()` 메소드를 사용하면, 가장 가까운 `yield` 키워드를 만날 때까지 코드를 실행할 수 있다.

###### 3-1. 제너레이터 함수 정의
```js
function* myGenerator() {
	yield 1; 
	yield 2; 
	yield 3; 
}
```
- `function*` 키워드와 함께 `yield` 키워드를 사용해 값을 생성한다.

###### 3-2. 제너레이터 호출
```js
function* fn() {
	console.log(1);
	yield 1;
	console.log(2);
	yield 2;
	console.log(3);
	console.log(4);
	yield 3;
	console.log(5);

	return "finish!!";
}

// iterator한 제너레이터 객체가 반환됨.
// 이 객체는 내부적으로 next()메소드를 가지고 있어, 사용 가능
const a = fn(); 

// 1 출력 -> next()는 {value:1, done: false} 반환 후 출력
console.log(a.next()); 

// 2 출력 -> next()는 {value:2, done: false} 반환 후 출력
console.log(a.next());

// 3,4 출력 -> next()는 {value:3, done: false} 반환 후 출력
console.log(a.next());

// 5 출력 -> next()는 {value: finish!!, done: true} 반환 후 출력
console.log(a.next());

// next()는 {value: undefined, done: true} 반환 후 출력
console.log(a.next());
```
- 제너레이터 함수 호출은 iterator 객체를 반환하며, `next()` 메서드를 호출해 하나씩 값을 얻을 수 있다.

###### 3-3. return 메소드를 사용한 제너레이터 중지 
```js
function* func() {
	console.log(11);
	yield 1;
	console.log(22);
	yield 2;
	console.log(33);
	console.log(44);
	yield 3;
	console.log(55);

	return "finish!!";
}

const a = func(); 

// 11 출력 -> next()는 {value:1, done: false} 반환 후 출력
console.log(a.next()); 

// 22 출력 -> next()는 {value:2, done: false} 반환 후 출력
console.log(a.next());

// 33,44 출력 -> next()는 {value: undefined, done: true} 반환 후 출력
console.log(a.return());

// iterator.next().return() 사용으로 함수 실행이 종료됨
// {value: undefined, done: true} 반환 후 출력
console.log(a.next());
```
- 제너레이터 객체의 return 메소드 `func().return()`를 사용하면, 즉시 done은 `true`가 되고, value는 `undefined`가 된다.
    - `iterator.next().return()`을 사용하면, 이후 `yield` 키워드를 만날 때까지 함수 내부의 코드를 실행하는 것이 아니라, 바로 그 **즉시 함수 실행을 종료**하기 때문에 value가 `undefined`가 된다.


#### 4. 제너레이터 활용 예시

> **무한 시퀀스 생성**
```js
function* infiniteSequence() {
    let i = 0;
    while (true) {
        yield i++;
    }
}
// 제너레이터는 값을 미리 계산해 놓지 않고, 요청 시 계산하기 때문에 효율적
const seq = infiniteSequence();
console.log(seq.next().value); // 0
console.log(seq.next().value); // 1
```

> **iterable 객체 생성**
```js
function* range(start, end) {
    for (let i = start; i <= end; i++) {
        yield i;
    }
}

// 이터러블(iterable) 객체를 반복(iterate)하기 위한 ES6에서 도입된 반복문
for (let value of range(1, 5)) {
     // range 내 yield i를 만나면, { value: i, done: 끝이 아니면 false, 맞으면 true } 반환하는 데 그 중 value 사용
    console.log(value); // 1, 2, 3, 4, 5
}
```
- 제너레이터는 기본적으로 `iterable` 객체이므로 `for...of` 루프에서 사용할 수 있다.

- `for...of` 루프는 **이터레이터 프로토콜**에 따라 동작하는데, 이터레이터 객체의 `next()` 메서드를 반복적으로 호출하면서 `value`와 `done` 속성을 읽어온다.
	1. `range(1, 5)`를 호출하면 **이터레이터 객체**가 반환된다.
	2. `for...of` 루프는 이터레이터 객체의 `next()` 메서드를 호출해 값을 하나씩 요청한다.
	3. `yield`가 반환한 값이 `value`에 할당된다.
		- 반환된 객체의 `{ value, done }` 구조 중 `value`를 `let value`에 할당된다.
		- `for...of`는 내부적으로 이터레이터 객체의 `next()`를 호출하고, 반환된 `{ value, done }` 구조체에서 `value`만 가져오기 때문에, `done`은 외부에 노출되지 않으며 접근할 수도 없다.
	4. 루프는 `i <= end` 조건이 더 이상 참이 아니게 될 때 종료된다.

- 이외에도 제너레이터는 `yield`를 사용해 중간 중간 작업을 멈추고 다시 시작할 수 있어, 비동기 흐름을 관리하기에도 좋다.