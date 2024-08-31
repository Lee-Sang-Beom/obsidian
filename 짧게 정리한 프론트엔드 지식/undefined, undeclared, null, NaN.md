
- 자바스크립트에서 `undefined`, `undeclared`, `null`, `NaN`는 각각 다르게 취급되며, 의미도 다르다.
	- 헷갈릴 수 있으니, 예제와 함께 보며 알아가보자.


#### 1. undefined

- **undefined**는 접근 가능한 스코프에 변수가 선언은 되었으나 아무런 값도 할당되지 않은 상태를 의미한다.

```js
let x;
console.log(x); // 출력: undefined -> 선언되었지만, 값이 할당되지 않았음

let y = undefined;
console.log(y); // 출력: undefined -> 명시적으로 `undefined` 값을 가지고 있음
```


#### 2. undeclared

- **undeclared**은 접근 가능한 스코프에 특정 변수가 선언조차 되어있지 않은 상태를 의미합니다. 
    - 변수를 선언하지 않고, 변수를 console.log로 출력하려 한다면 에러가 발생하는 것이 그 예시이다.

```js
console.log(z); // ReferenceError: z is not defined
```


#### 3. null

- null은 변수를 선언하고, 변수에 null이란 빈 값을 할당하는 것이다. 
	- 프로그래머가 변수가 비어있음을 명시적으로 표현할 때 사용하며, **의도적으로 값이 없음**을 나타내는 값이다.
	- null이라는 빈 값을 할당하면, 해당 변수는 객체가 된다. (`typeof null` 시, `object`라고 출력됨)

```js
let a = null;
console.log(a); // 출력: null
console.log(typeof a); // 출력: object
```



#### 4. NaN(Not a Number)

- NaN은 숫자형 계산에서 잘못된 연산이 발생했음을 나타낸다.
	- 주로 숫자가 아닌 값을 숫자로 변환하려 할 때나, 잘못된 수학적 연산의 결과로 발생한다.
    - 예를들면, ```hello```라는 문자열을 2으로 나눈다거나 할 때 발생한다.

```js
let b = "hello" / 2;
console.log(b); // 출력: NaN

console.log(Math.sqrt(-1)); // 출력: NaN -> 음수의 제곱근을 계산하려 함
console.log(isNaN("hello" / 2)) // 출력: true
```