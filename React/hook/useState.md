
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


#### 3. 예제 1 (문자열 관리 - `string`)

- 사용자가 입력하는 특정 검색어를 관리할 때 유용하다.
	- 즉, `Form`에서 사용하기 용이하다.
	- **입력 요소가 많을 때**는, 여러 개의 `useState`를 호출하는 것 보다는, `react-hook-form`을 사용하는 것이 좀 더 편하다고 생각한다.

```tsx
const [chemSearchKeyWord, setChemSearchKeyWord] = useState<string>("");
```

#### 4. 예제 2 (객체 관리 - `object`)

- `useState`는 단일 `string`, `number` 외에도 객체 또한 관리할 수 있다.

- 초기화는 원하는 객체값으로 세팅해주면 된다.
```tsx
const [companySearchObj, setCompanySearchObj] =
useState<SearchCompanyCommand>({
  page: 0,
  size: 5,
  sort: "companyNm",
  orderBy: "desc",
  companySearchType: "ALL",
  searchKeyWord: "",
});
```

- `setState`의 경우, 아래와 같이 사용할 수 있다.
	- `setState` 시 중요한 것은, 새로운 값으로 갱신할 때 이전 상태`(prevState)`를 고려해야 한다는 점이다.
	- `setState` 에 콜백 함수를 전달할 경우, `prev` 인자를 통해 이전 값이 무엇이었는지를 불러올 수 있다.
		- 아래와 같이, `...prev`로 이전 값 세팅을 진행한 후, 덮어쓰기 할 데이터를 입력해주면 객체의 모든 값 중 변경된 값만 재적용이 가능하다.
```tsx
  setCompanySearchObj((prev) => {
	return {
	  ...prev,
	  page: 0,

	  companySearchType:
		selectCompanySearchType,
	  searchKeyWord: searchCompanyKeyWord,
	};
  });
```

#### 5. `useState`에서, `setState` 사용 시 주의할 점 (1)

- `setState`는 기본적으로 상태 변경 시 비동기로 동작한다.
```tsx

const [count, setCount] = useState<number>(0);

return (
	{/* ... */}
	<button
	onClick={()=>{
		setCount(prev => prev+1);
		if(count === 5) {
			console.log('count is 5 : ', count);
		}
	}}>
	</button>
)
```

- 때문에, 위의 코드에서 버튼을 5회 클릭해 `count` 값이 5가 되면 바로 `console.log(...)`가 출력되지 않고, 1번 더 클릭해야 출력된다.
	- 즉, `setCount(...)`가 비동기적으로 동작하여, `if(count)===5`조건문이 **이전 값**을 바라보면서 통과되지 않는 것이다.


#### 5. `useState`에서, `setState` 사용 시 주의할 점 (2)

- `setState`는 기본적으로 상태 변경 시 비동기로 동작한다고 언급했다.
	- 그럼 아래 코드에서 버튼을 클릭하면 `count`는 뭐라고 출력될까?
```tsx

const [count, setCount] = useState<number>(0);

return (
	{/* ... */}
	<button
	onClick={()=>{
		setCount(prev => prev+1);
		if(count === 5) {
			console.log('count is 5 : ', count);
		}
	}}>
	</button>
)
```

- 때문에, 위의 코드에서 버튼을 5회 클릭해 `count` 값이 5가 되면 바로 `console.log(...)`가 출력되지 않고, 1번 더 클릭해야 출력된다.
	- 즉, `setCount(...)`가 비동기적으로 동작하여, `if(count)===5`조건문이 **이전 값**을 바라보면서 통과되지 않는 것이다.

```tsx
// ...
  const [count, setCount] = useState<number>(0);
  return (
    <button
      onClick={() => {
        setCount(count + 1);
        setCount(count + 2);
        setCount(count + 3);
      }}
    >
      {count}
    </button>
  );
```

- 정답은 `3`이다. `setState`문은 비동기적으로 동작하며, 변경된 값들을 모아 한번에 업데이트를 진행하여 렌더링을 줄이고자 **배치(Batch) 기능**을 사용해 비동기로 작동한다.
	- 때문에, 위와 같이 코드를 작성할 경우 제일 마지막 코드만 작동하는 것이다.
	- 그렇다면, 이전 값을 계속 더하려면 어떻게 해야할까?

```tsx
// ...
return (
    <button
      onClick={() => {
        setCount((prev) => prev + 1);
        setCount((prev) => prev + 2);
        setCount((prev) => prev + 3);
      }}
    >
      {count}
    </button>
  );
```

- 정답은 react의 `setState` 사용 시, 이전 상태를 가져오는 `prev` **매커니즘**을 사용하는 것이다.
	- 이 매커니즘은 `React`에 내장되어 있으며, 이전 상태를 직접적으로 참조하고 이전 상태를 새로운 상태를 계산하는 데 활용하는 데에 유용하다.

- **클래스형 컴포넌트**에서는 `setState` 함수가 다음과 같은 구조로 이루어져 있어 콜백 기능을 제공한다고 한다.
	- 클래스형 컴포넌트의 `setState`는 두 번째 매개변수로 콜백 함수를 전달할 수 있다. 콜백 함수는 함수의 상태가 업데이트될 때마다 호출된다.
```tsx
this.setState(newState, callbackFunction)
```

- 하지만, **함수형 컴포넌트**에서는 이야기가 조금 다르다.
```tsx
const [state, setState] = useState(); 
setState(newState, callbackFunction); // 이 경우 콜백 함수가 호출되지 않고 경고가 발생
```

	-  **함수형 컴포넌트**에서 사용하는 `useState`에는 순차적 동작을 위한 콜백 함수를 넣어줄 수 없다.
		- `[ex: setState(새 상태, 콜백함수)]`
	- Promise 또한 반환하지 않기 때문에, `async, await`을 사용할 수 없다.
	- 그래서, 클래스형 컴포넌트에서 사용한 `callback`처럼 코드 흐름을 구현하기 위해, 별도로 등장한 `hook`이 있는데, 그것이 `useEffect`이다.
