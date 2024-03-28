
#### 1. `useState`란?

-  **useState**는 함수형 컴포넌트의 상태를 정의 및 관리할 수 있도록 도와주는 `react hook`이다.
	- `state`는 일종의 변수이다.
	- `state`는 일반 변수와 다르게, 값이 변하게 되면 **렌더링**이 일어나게 된다. 
		- 즉, 해당 `state`값이 변하게 되면 연관 컴포넌트들이 다시 렌더링되면서 화면이 바뀌게 된다.


#### 2. `useState`의 구조

- `useState`는 구조 분해 할당을 이용한 선언 방식을 사용한다.

- **인자 1**
	- 구조 분해 할당 대상으로 설정된 첫 번째 원소에는 `상태값(state)`이 반환된다.
	
- **인자 2**
	- 구조 분해 할당 대상으로 설정된 두 번째 원소에는 상태값을 업데이트하기위한 `setter` 함수가 반환된다. 
		- `Dispatch<SetStateAction<Type>>` 타입을 가진다.

```tsx
const [state, setState] = useState(initialState);
```


#### 3. 예제 1
