
#### 1. Promise란?

- Promise는 비동기 동작을 처리하기 위해 다루는 객체로, 비동기 연산이 종료된 후의 결과를 알 수 있도록 한다.
- 콜백 지옥을 해결하여 코드 유지보수성을 높이고, 비동기 작업을 좀 더 편리한 방식으로 사용할 수 있게 도움을 준다.
```js
const myPromise = new Promise((resolve, reject) => {
  const success = true; // 설정값 (api 요청 성공 or 실패라고 생각해보자)
  
  if (success) {
    resolve("The operation was successful!");
  } else {
    reject("The operation failed.");
  }
});

myPromise
  .then((message) => {
    console.log("Fulfilled: ", message);
  })
  .catch((error) => {
    console.error("Rejected: ", error);
  });

```

#### 2. Promise의 상태

- Promise는 대표적으로 3가지 상태를 가질 수 있다.
    1. **Pending(대기)** : Promise가 처음 생성되었을 때, 아직 작업이 완료되지 않은 상태 (비동기 처리 미완료)
    2. **Fulfilled(이행)** : 비동기처리가 완료되어 Promise 결과를 반환한 상태
		- 이 때, Promise는 이행된 결과 값을 가진다.
		  
    3. **Rejected(실패)** : 비동기 처리가 실패하거나 오류가 발생할 때의  상태
	    - 이 때, Promise는 실패 원인인 오류 정보를 가진다.

```jsx
function getData() {
	return new Promise(function(resolve, reject) {
		$.get('url 주소/products/1', function(response) {
		if (response) {
			resolve(response);
		}
		reject(new Error("Request is failed"));
		});
	});
}
```
- 객체인 Promise를 인스턴스화시키기 위해서는 `new` 연산자를 사용해야 한다.
	- Promise의 argument로는 콜백 함수를 넘겨줄 수 있는데, 이 함수는 내부에서 비동기 처리를 수행하는 함수이며, 전달한 콜백함수에서의 파라미터로는 `resolve`와 `reject`가 존재한다.
	  
	- 해당 함수 내에서 비동기 처리로직을 구현한 후, 정상적으로 처리가 되었다면 `resolve`함수를 호출하고, 실패했다면 `reject`함수를 호출하면 된다.


#### 3. `.then()`

- 비동기처리를 호출하는 측에서는, Promise 객체의 후속처리 메소드인 `.then()`과 `.catch()`를 통해 비동기 처리결과를 전달받아 처리할 수 있다.
	- 이 후속처리메소드 둘 다 Promise를 반환한다.

```js
const promise1 = new Promise((resolve, reject) => {
	resolve('Success!');
	reject('fail');
});

promise.then(
  (result) => {
    console.log("Success:", result);
  },
  (error) => {
    console.log("Failed:", error);
  }
);
```
- 먼저, `then` 메소드는 `Promise`가 **이행(Fulfilled)** 되었을 때만 호출되며, 두 개의 콜백 함수를 파라미터로서 전달받는다.
	- 첫번째 콜백함수는 `resolve()`가 호출되어 성공일때 실행되고, 다음은 `reject()`가 호출되어 실패일 때 실행된다.
	- 두 번째 콜백함수는 선택사항이며, 거부된 경우를 처리할 수 있다. (`.catch()`로 대체 가능)

- 예를 들어, 만약 Promise 인스턴스를 생성할 때 인자로 작성한 콜백함수가, 비동기 처리결과로서 `resolve()`를 호출했다면 `.then()` 메소드의 첫번째 파라미터로 작성한 함수가 실행되는 것이다.


#### 4. `.catch()`

- `.catch()` 메소드는 Promise 비동기 처리 중 `reject()`가 발생했을 때나, `.then()` 메소드 실행 중 에러가 발생할 때 호출된다.
```js
promise
  .then((result) => {
    console.log("Success:", result);
  })
  .catch((error) => {
    console.log("Failed:", error);
  });
```

- Promise에서의 에러 처리 방법으로, `.then()` 메소드의 두 번째 인자로 전달된 함수를 이용하거나, 해당 `.catch()` 메소드를 사용하는 방법이 있다. 그런데 보통은, `.catch()` 메소드가 더 많이 에러를 잡아낼 수 있다고 한다.


#### 5. Promise Chain

```js
someAsyncFunction()
  .then((result1) => {
    // 첫 번째 비동기 작업이 성공적으로 완료된 후 실행
    return anotherAsyncFunction(result1); // 두 번째 비동기 작업을 실행
  })
  .then((result2) => {
    // 두 번째 비동기 작업이 성공적으로 완료된 후 실행
    return yetAnotherAsyncFunction(result2); // 세 번째 비동기 작업을 실행
  })
  .then((result3) => {
    // 세 번째 비동기 작업이 성공적으로 완료된 후 실행
    console.log('All tasks completed:', result3);
  })
  .catch((error) => {
    // 위에서 발생한 어떤 오류든 여기에서 처리
    console.error('An error occurred:', error);
  });

```
- Promise 사용 과정에서 사용되는 new 생성자(인스턴스 생성), `.then()`, `.catch()` 후속처리 메소드들은 모두 Promise 객체를 반환한다.
	- 따라서, **Promise Chain**이라는 것을 사용할 수 있다.
	  
- **Promise Chain**은 여러 개의 `Promise`를 순차적으로 연결하여 비동기 작업을 처리하는 방법을 의미한다.
	- Promise를 사용하면 비동기 작업이 완료된 후 다음 작업을 실행할 수 있으며, 여러 작업을 순서대로 처리할 때 Promise Chain을 사용할 수 있다.
	  
	- 이는, 비동기 함수결과로 또다시 비동기 함수를 호출해야하는 경우에 `.then()` 후속처리 메소드를 연결하는 방식으로 사용할 수 있다.


> **장점**
- **가독성**: 비동기 작업을 연속적으로 수행하는 로직을 명확하게 표현할 수 있습니다.
- **에러 처리**: 체인 중간에 발생한 모든 오류를 하나의 `catch` 블록에서 처리할 수 있습니다.
- **순차적 실행**: 각 비동기 작업이 이전 작업이 완료된 후에 순차적으로 실행되도록 할 수 있습니다.

> **주의할 점**
- 주의할 점을 꼽아보다면, `.then()` 메소드로 함수 실행 순서를 정할 수 있기 때문에 연속으로 `.then()` 메소드를  많이 사용하는 상황이 발생할 수 있다는 점이다.
	- 이는 콜백지옥와 같이, 작성하기 어렵고 가독성이 떨어지는 코드가 만들어지도록 한다. (뭐든 남용은 좋지 않다.)

