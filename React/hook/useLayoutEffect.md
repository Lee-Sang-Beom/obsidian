
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
	- `useEffect`: 업데이트된 컴포넌트가 화면에 그려지고 난 다음(화면이 업데이트된 후), `effect`가 실행된다.
	- `useLayoutEffect`: `effect`가 먼저 실행되고, 업데이트된 컴포넌트가 화면에 그려진다.

- `useLayoutEffect`를 사용하면, `effect`실행 후 화면이 업데이트되기 때문에, 사용자에게 보여지는 UI 변화를 좀 더 정교하게 다룰 수 있다.


#### 3. 사용해보기

- 아래 예제에서 `useLayoutEffect`가 `useEffect`보다 먼저 출력되는 것을 확인할 수 있다.

```tsx
"use client";
import { useEffect, useId, useLayoutEffect, useState } from "react";
export default function Component() {
  const [count, setCount] = useState<number>(0);

  useEffect(() => {
    // 먼저실행됨
    console.log("useEffect and count: ", count);
  }, [count]);

  useLayoutEffect(() => {
    // 먼저실행됨
    console.log("useLayoutEffect and count: ", count);
  }, [count]);

  const handleCount = () => {
    setCount((prev) => prev + 1);
  };
  return (
    <div>
      <p>count: {count}</p>
      <button onClick={handleCount}>update</button>
    </div>
  );
}
```
![[useLayoutEffect(1).png]]

- 
