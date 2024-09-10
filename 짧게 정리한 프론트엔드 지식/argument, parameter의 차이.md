> [참고자료: cloud_oort.log](https://velog.io/@cloud_oort/Parameter%EC%99%80-Argument-%EC%9D%B8%EC%9E%90%EC%99%80-%EC%9D%B8%EC%88%98-%EC%9A%A9%EC%96%B4-%EA%B5%AC%EB%B6%84)


#### 1. 용어 정리

- 자바스크립트에서 **Parameter(매개변수)** 와 **Argument(인수)** 는 함수 호출과 관련된 용어로, 이 둘은 함수 정의와 함수 호출 시 서로 다른 역할을 한다.

1. **Parameter (매개변수)**
	- **함수를 정의할 때** 사용하는 변수
	- 함수가 호출될 때, 전달받는 값을 저장하는 "placeholder" 역할을 한다.
	- 함수 내부에서 사용되는 값을 참조할 때 사용된다.

2. **Argument (인수)**
	- **함수를 호출할 때** 함수에 전달되는 실제 값
	- 매개변수(parameter)에 전달되어 함수가 동작하는 데 필요한 데이터를 제공한다.


#### 2. 차이

- **Parameter**는 함수가 정의될 때, 해당 함수가 받을 수 있는 값의 이름을 지정하는 것이다.
- **Argument**는 함수가 호출될 때, 매개변수로 전달되는 실제 값이다.

> **예제 1**
```js
// 매개변수(Parameter)는 x, y입니다.
function add(x, y) {
  return x + y;
}

// 인수(Argument)는 3과 5입니다.
let sum = add(3, 5);

console.log(sum); // 출력: 8
```
- `x`와 `y`는 **매개변수(Parameters)** 로 함수가 정의될 때, 해당 함수가 받을 수 있는 전달 요소의 이름을 지정한 것이다.
- `3`과 `5`는 **인수(Arguments)** 로 함수가 호출될 때 전달된 실제 값이다.


> **예제 2**
```js
function add (a, b) {
 return a + b;
}

const x = 2;
const y = 3;

const z = add(x, y)

console.log(z)
```
- 이 예제에서는, `add()`함수를 호출할 때 '인자'를 상수 값이 아닌, 다른 변수 x와 y의 값으로 넘겼다.  
	- `add()`함수 내의 매개변수는 그대로 a와 b이다. 그러나 `x`, `y`라는 변수도 인자로 넘기는 데에 사용되었다.  

- 이렇게, 인자가 상수가 아닌 특정 변수의 값으로 넘겨지는 경우, 이 특정 변수를 **'실제 매개 변수(actual parameter)'** 라 부른다.
	- 그리고, 처음에 이야기 된 **'매개 변수'** 인 `a`와 `b`는 실제 매개 변수와 구분 시켜 주기 위해 **'형식 매개 변수(formal parameter)'** 라 부른다.


#### 3. 요약

- **Parameter**: 함수를 정의할 때 사용되는 변수.
- **Argument**: 함수를 호출할 때 전달되는 실제 값

