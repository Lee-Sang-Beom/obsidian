
#### 1. `useLayoutEffect`의 사용방법

- 사용 방법은 `useEffect`와 동일하다.
	- 1번째 인자: `effect`라고 불리는 **콜백 함수**
	- 2번째 인자: 의존성 배열
```tsx
useEffect(()=>{
// effect...
},[state]);

useLayoutEffect(()=>{
// effect...
},[state]);
```

- `effect`가 실행되는 기능 또한 `useEffect` 와 거의 동일하다.
	- 컴포넌트가 처음 렌더링될 때
	- 의존성 배열 내 값이 업데이트될 때


#### 2. `useEffect`와 `useLayoutEffect`의 차이

- `useLayoutEffect`와 `useEffect`는 대부분 동일하지만,  **`effect`가 실행되는 시점**에서 차이가 있다.

1. `useEffect`
	- 업데이트된 컴포넌트가 화면에 그려지고 난 다음, `effect`가 실행된다.

2. `useLayoutEffect`
	- `effect`가 먼저 실행되고, 업데이트된 컴포넌트가 화면에 그려진다.

