
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
	- `setState` 시 중요한 것은, 새로운 값으로 갱신할 때 이전 상태`(prevState)`를 고려해
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