
#### 1. Overview

- JavaScript에서 **blocking**과 **non-blocking**의 차이는 코드 실행 흐름이 멈추는지 여부에 따라 나뉜다. 
	- 이를 이해하려면 JavaScript의 실행 모델인 **싱글 스레드**와 **이벤트 루프**에 대한 기본 개념이 필요하다.


#### 2. Blocking

- **Blocking**은 코드 실행 흐름을 멈추고, 특정 작업이 완료될 때까지 기다리는 동작을 의미한다. 
	- 이 동안 JavaScript 엔진은 다른 작업을 할 수 없으며, 해당 작업이 끝날 때까지 다음 코드가 실행되지 않는다.

```js
// 블로킹 예시
function blockingTask() {
  // 작업이 오래 걸린다고 가정
  let start = Date.now();
  while (Date.now() - start < 5000) {
    // 5초 동안 블로킹
  }
  console.log("Blocking task finished.");
}

blockingTask();
console.log("This will print after the blocking task.");
```
- 위의 예에서 `blockingTask()` 함수가 완료될 때까지 JavaScript는 다른 작업을 실행하지 못하고 기다린다.
	- 이 방식은 CPU가 작업을 기다리느라 효율적이지 못하고, UX도 저하된다.


#### 3. Non-Blocking

- **Non-blocking**은 코드가 실행 중인 작업이 끝나기를 기다리지 않고, 다른 작업을 병렬로 처리할 수 있게 하는 방식이다.
	- JavaScript는 비동기 작업을 처리하기 위해 **콜백 함수**나 **Promise**, **async/await** 등의 패턴을 사용하는데, 이런 방식은 시간이 오래 걸리는 작업을 백그라운드에서 처리하고, 완료되면 그 결과를 처리할 수 있도록 한다.

```js
// 비동기 함수 (non-blocking 예시)
function nonBlockingTask() {
  setTimeout(() => {
    console.log("Non-blocking task finished.");
  }, 5000); // 5초 후에 실행
}

nonBlockingTask();
console.log("This will print before the non-blocking task finishes.");

```

- 위의 예에서 `nonBlockingTask()`는 5초 후에 작업을 완료하지만, 그 사이에 다음 코드인 `console.log()`가 즉시 실행된다. 
	- JavaScript 엔진은 비동기 작업을 이벤트 루프를 통해 처리하고, 5초가 지나면 그 작업이 완료된 시점에 콜백함수를 실행시킨다.


#### 4. 차이

- **Blocking**과 **Non-Blocking**은 현재 수행 중인 코드가, 다음 작업 과정을 **막는 경우가 발생하는지, 발생하지 않는지**로 구분할 수 있다.
    - **Blocking**: 다음 작업을 처리하기 위해, (호출된 함수에서) 진행 중인 작업이 끝날 때 까지 기다려야 하는 경우
        - 혹은 함수 호출 시 제어권을 넘기는 경우, Blocking으로 구분할 수 있다.
      
	- **Non-Blocking**: 현재 작업의 처리 여부와 상관없이 다음 작업을 할 수 있는 경우
        - 혹은 함수 호출 시 제어권을 넘기지 않는 경우, Non-Blocking으로 구분할 수 있다.


>  **※ 제어권**
- 자바스크립트는 싱글 스레드 기반인, **Non-Blocking** 언어이다.
    - 자바스크립트는 말그대로 **싱글 스레드**를 기반으로 하고 있기 때문에, **동기적 코드만으로 프로그램을 구성하면** 문제가 발생할 수 있다.
      
    - 코드를 실행할 때, 시간이 오래 걸리는 작업을 할 때를 예시로 들 수 있다.
	    - **특정 라인의 코드 실행 때문에 다음 코드를 실행할 수 없는 현상**이 발생하면, 이 때 브라우저는 마치 정지된 것처럼 보이는데, 이것을 **Blocking** 현상이라고 한다.
		- 이 현상은, 브라우저가 **제어권**을 돌려받아야, 다음 코드를 수행할 수 있기 때문에 발생한다.

- **제어권**은 자신(함수)의 코드를 실행할 권리 같은 것으로 이해할 수 있다.
	- 제어권을 가진 함수는 자신의 코드를 끝까지 실행한 후, 자신을 호출한 함수에게 돌려준다.