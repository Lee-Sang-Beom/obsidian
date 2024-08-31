
#### 1. Promise란?

- Promise는 콜백지옥을 해결하기 위해 등장한 비동기 동작을 다루는 객체로, 비동기연산이 종료된 후의 결과를 알 수 있도록 한다.

- 콜백 지옥을 해결하여 코드 유지보수성을 높이고, 비동기 작업을 좀 더 편리한 방식으로 사용할 수 있게 도움을 준다.


#### 2. 상태
- Promise는 대표적으로 3가지 상태를 가진다.
    1. 비동기 처리가 완료되지 않은 **pending(대기)** 상태
    2. 비동기처리가 완료되어 Promise 결과를 반환한 **fulfilled(이행)** 상태
    3. 마지막으로 비동기 처리가 실패하거나 오류가 발생할 때의 **rejected(실패)** 상태

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
- 객체인 Promise를 인스턴스화시키기 위해서는 new 연산자를 사용해야 한다.
- 이 때 인자로 콜백 함수를 넘겨주는데, 이 함수는 내부에서 비동기 처리를 수행하는 함수이며, 파라미터로 resolve와 reject를 가진다.
- 해당 함수 내에서 비동기 처리로직을 구현한 후, 정상적으로 처리가 되었다면 resolve함수를 호출하고, 실패했다면 reject함수를 호출하는 것이 주요 개념이다.


#### 3. 후속처리메소드 then

- 비동기처리를 호출하는 측에서는, Promise 객체의 후속처리 메소드인 then과 catch를 통해 비동기 처리결과를 전달받아 처리할 수 있다. 이 후속처리메소드 둘 다 Promise를 반환한다.

    ```
    const promise1 = new Promise((resolve, reject) => {
    resolve('Success!');
    reject('fail');
    });

    promise1.then((value) => {console.log(value)}, (error) => {console.log(error)});
    ```
- then은 두 개의 콜백 함수를 인자로 전달받는데, 첫번째 콜백함수는 resolve가 호출되어 성공일때 실행되고, 다음은 reject가 호출되어 실패일 때 실행된다.

- 예를 들어, 만약 Promise 인스턴스를 생성할 때 인자로 작성한 콜백함수가, 비동기 처리결과로서 resolve()를 호출했다면 then메소드의 첫번째 파라미터로 작성한 함수가 실행되는 것이다.


#### 4. 후속처리메소드 catch

- catch 메소드는 비동기 처리 혹은 then 메소드 실행 중 에러가 발생하면 호출된다.
- Promise 에러처리방법으로, then메소드의 두번째 인자로 전달된 함수를 이용하거나, 해당 catch문을 사용하는 방법이 있지만, catch메소드가 더 많이 에러를 잡아낼수있다고 한다.


#### 5. Promise Chain

- new 생성자(인스턴스 생성), 후속처리 메소드 then, catch모두 Promise 객체를 반환하기 때문에, Promise 체이닝을 사용할 수 있다.
- 이는, 비동기 함수결과로 또다시 비동기 함수를 호출해야하는 경우에 then 후속처리 메소드를 연결하는 방식으로 사용할 수 있다.

- 하지만, then으로 함수 실행 순서를 정할 수 있기 때문에 연속으로 then()을 많이 사용하는 상황이 발생할 수 있고, 이는 콜백지옥와 같이, 작성하기 어렵고 가독성이 떨어지는 코드가 만들어지도록 한다.