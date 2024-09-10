
#### 1. 정의

- **클로저**는 반환된 내부함수와 내부함수가 선언되었던 렉시컬 환경의 조합으로 정의할 수 있다.
	- 자바스크립트의 클로저는, 함수가 생성될 때 해당 함수가 선언된 **렉시컬 환경(Lexical Environment)** 을 기억하고, 그 환경에 접근할 수 있는 함수이다.
	- 함수가 선언된 환경의 스코프를 기억해, 함수가 반환된 후에 스코프 밖에서 실행될때도, 기억한 스코프에 접근할 수 있도록 만드는 문법이라는 뜻이다.

- 클로저는 다음과 같은 상황에서 주로 발생한다
	- 함수가 다른 함수 내부에 정의될 때, 내부 함수는 외부 함수의 변수에 접근할 수 있다.
	- 외부 함수가 종료된 후에도 내부 함수는 외부 함수의 변수에 접근할 수 있다.
		- 이는 외부 함수가 실행되고 내부함수를 반환한 후에 **외부함수가 콜스택에서 사라진 상황**에서, 반환된 내부함수를 실행할 때 콜스택에서 **사라진 외부 함수의 스코프에 접근할 수 있는 상황** 자체를 생각할 수 있는 것이다.


#### 2. 예제

```js
function outerFunction(outerVariable) {
  return function innerFunction(innerVariable) {
    console.log('Outer Variable:', outerVariable);
    console.log('Inner Variable:', innerVariable);
  };
}

const newFunction = outerFunction('outside');
newFunction('inside');
```

1. `outerFunction`이 호출되면 `outerVariable` 값이 `'outside'`로 설정된다.
2. `outerFunction`은 내부 함수인 `innerFunction`을 반환한다.
	- 이때 `outerFunction`은 끝났지만, `innerFunction`은 여전히 변수 `outerVariable`을 기억한다.

3. `newFunction('inside')`를 호출하면 내부에서 `innerFunction`이 실행되며, `outerVariable`을 `'outside'`로, `innerVariable`을 `'inside'`로 출력한다.

   
#### 3. 특징

- **변수 캡처**: 외부 함수가 종료된 후에도, 내부 함수는 외부 함수의 변수에 접근할 수 있다.
- **데이터 은닉**: 클로저를 활용해 함수 내에서만 접근 가능한 변수를 만들 수 있어, 정보 은닉과 같은 패턴을 구현할 수 있습니다.
- 정리하면, **클로저**는 **전역 변수의 사용을 억제**하고, **내부 변수와 함수같은 정보를 은닉**하기 위해 사용할 수 있다. 


#### 4. 용도

- **정보 은닉**: 특정 변수나 함수를 외부에 노출시키지 않고, 함수 내부에서만 사용되도록 할 수 있다.
- **콜백 함수**: 클로저는 비동기 코드나 이벤트 핸들러에서 유용하게 사용된다.
- **함수형 프로그래밍**: 클로저는 함수형 프로그래밍의 중요한 개념으로, 함수가 상태를 기억하거나 조작하는 데 도움을 준다.

