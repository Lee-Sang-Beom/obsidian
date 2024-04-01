
#### 1. `useLayoutEffect`란?

- ㅋㅋ
- ㅋㅋ


- 사용 방법은 `useEffect`와 동일하다.
	- 1번째 인자: `effect`라고 불리는 **콜백 함수**
	- 2번째 인자: 의존성 배열
```tsx
useEffect(()=>{
// ...
},[state]);

useLayoutEffect(()=>{
// ...
},[state]);
```

- `effect`가 실행되는 기능 또한 `useEffect` 와 거의 동일하다.
	- 컴포넌트가 처음 렌더링될 때
	- 의존성 배열 내 값이 업데이트될 때