
#### 1. `useReducer`란?

- `useReducer`는 `useState`를 대체할 수 있는 함수이다.
	- `reducer`를 사용하여 컴포넌트의 상태 로직을 관리하는 데 사용하며, 주로 `useState`보다 복잡한 상태 로직을 처리해야할 때 사용한다고 한다.
	
- 작성자 본인은 평소 습관적으로 `useState`를 사용하다가 `useReducer`에 대한 내용을 살펴보면 당장에 잘 이해가 되지 않았다.
	- 본인은 몇 개의 프로젝트를 진행하면서, **많은 입력 Form을 관리**해야 하는 상황이 있다면, `react-hook-form`을 사용했었기 때문에 `useReducer`의 필요성을 느끼지 못했었다.
	- 그래도 기회가 되면 나중에 다시 공부해보고 실 프로젝트에 적용해보고싶다.


#### 2. `useReducer` 사용하기

- `useReducer`의 구성요소는 크게 3가지가 있다.
	1. `reducer`: `state`를 업데이트시켜주는 역할의 **함수**
	2. `dispatch`: `reducer`에게 보내는 일련의 **요구**
	3. `action`: `reducer`에게 보내는 일련의 요구에 대한 **내용**

- `useReducer`의 초기 정의는 컴포넌트 최상단에서 다음과 같이 이루어진다.
	- `const [stateName, dispatch function] = useReducer(reducer func, state 초기값);`
	
	- **첫 번째 인자**: `useReducer`는 배열을 반환하는데, 첫 번째 인자는 새로 만들어진 `state`이다.
		- `useReducer`라고 별반 `useState`와 다르진 않으나, 이 `state`는 `reducer`함수를 통해 **변경**하여야 한다.

	- **두 번째 인자**: `useReducer`가 만들어준 `dispatch`함수이다.
		- `useState`의 `setState dispatch`와 다르게 `reducer` **함수를 실행시킨다는 점**에서 차이가 있다.


- 