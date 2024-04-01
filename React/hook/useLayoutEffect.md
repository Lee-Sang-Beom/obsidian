
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


#### 2. `useEffect`와 `useLayoutEffect`의 차이 1
 
- `useLayoutEffect`와 `useEffect`는 대부분 동일하지만,  **`effect`가 실행되는 시점**에서 차이가 있다.
	- `useEffect`: 업데이트된 컴포넌트가 화면에 그려지고 난 다음(화면이 업데이트된 후), `effect`가 실행된다.
	- `useLayoutEffect`: `effect`가 먼저 실행되고, 업데이트된 컴포넌트가 화면에 그려진다.
		
- `useLayoutEffect`를 사용하면, `effect`실행 후 화면이 업데이트되기 때문에, 사용자에게 보여지는 UI 변화를 좀 더 정교하게 다룰 수 있다.

- **즉**, 실행 순서는 `useLayoutEffect -> 컴포넌트 재렌더링 및 화면 업데이트 -> useEffect`이다.

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


#### 3. `useEffect`와 `useLayoutEffect`의 차이 2

- 또한, `useEffect`의 `effect`는 컴포넌트 렌더링에 따라 **화면이 업데이트**된 후, 다른 작업을 **blocking**하지 않고 비동기적으로 실행된다.

- 반면, `useLayoutEffect`는 화면 업데이트 전에 `effect`가 먼저 **동기적**으로 수행된다.
	- 이 말은, `useLayoutEffect`의 `effect`문이 실행되는 동안에는 화면이 업데이트되지 않는다. (blocking)
	- 무거운 작업이 있을 때는, 사용에 유의해야 한다.


#### 4. 예제 1. `useEffect`

```tsx
"use client";
import { useEffect, useId, useLayoutEffect, useRef, useState } from "react";

function getNumbers() {
  return [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15];
}
export default function Component() {
  const ref = useRef<HTMLDivElement | null>(null);
  const [numbers, setNumbers] = useState<number[]>([]);

  useEffect(() => {
    const nums = getNumbers();
    setNumbers(nums);
  }, []);

  // 페이지 로드 시, 마지막 숫자가 있는 곳까지 스크롤을 맨 아래로 구성
  useEffect(() => {
    if (!numbers.length) {
      return;
    }

    // 의도적 딜레이
    for (let i = 0; i < 300000000000000000; i++) {}

    // 의도적 딜레이 때문에, 데이터가 로드되어 화면에 표시되고, for문이 끝나야 스크롤 이벤트가 발생하는 것을 확인 가능
    if (ref && ref.current) {
      ref.current.scrollTop = ref.current.scrollHeight;
    }
  }, [numbers]);

  return (
    <div
      ref={ref}
      style={{
        height: "300px",
        border: "1px solid black",

        // 숫자 표시 영역을 스크롤할 수 있도록
        overflow: "scroll",
      }}
    >
      {numbers.map((number, idx) => {
        return <p key={number}>{number}</p>;
      })}
    </div>
  );
}
```

- 위의 예제는, 페이지 로드 시 마지막 숫자가 있는 영역까지 **스크롤을 자동으로 내리게끔 구성한 예제**이다.
	- `useEffect`는 콜백 함수로 전달한 `effect`를 컴포넌트가 화면에 그려진 후,(화면 업데이트 후) 비동기적으로 실행하는 특징이 있다.
	- 때문에, `useEffect`내의 복잡한 로직이 구성되어 있다면, **본 예제에서 원하는 UI 이벤트가 의도치 않게 작동**할 수 있다.

- 이는 사용자에게 정교한 UI를 보여주어야 할 경우, `useEffect`의 단점을 여실히 보여준다.
	- 그렇다면 **정확한 시점에, 정교하게 UI를 변경**하려면 어떻게 해야할까?


```tsx
"use client";
import { useEffect, useId, useLayoutEffect, useRef, useState } from "react";

function getNumbers() {
  return [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15];
}
export default function Component() {
  const ref = useRef<HTMLDivElement | null>(null);
  const [numbers, setNumbers] = useState<number[]>([]);

  useEffect(() => {
    const nums = getNumbers();
    setNumbers(nums);
  }, []);

  // 페이지 로드 시, 마지막 숫자가 있는 곳까지 스크롤을 맨 아래로 구성
  useLayoutEffect(() => {
    if (!numbers.length) {
      return;
    }

    // 의도적 딜레이
    for (let i = 0; i < 3000000000; i++) {}

    // 의도적 딜레이 때문에, 데이터가 로드되어 화면에 표시되고, for문이 끝나야 스크롤 이벤트가 발생하는 것을 확인 가능
    if (ref && ref.current) {
      ref.current.scrollTop = ref.current.scrollHeight;
    }
  }, [numbers]);

  return (
    <>
      <button onClick={() => setNumbers([])}>reset</button>
      <div
        ref={ref}
        style={{
          height: "300px",
          border: "1px solid black",

          // 숫자 표시 영역을 스크롤할 수 있도록
          overflow: "scroll",
        }}
      >
        {numbers.map((number, idx) => {
          return <p key={number}>{number}</p>;
        })}
      </div>
    </>
  );
}
```

- `useLayoutEffect`를 사용할 경우, 화면이 업데이트되기 전에 `effect`문이 실행되기 때문에, UI를 특정 변경사항을 반영한 후 표시해야하는 상황에 굉장히 유용하게 사용할 수 있다.
	- 본 예제에서는, **딜레이**없이 이미 스크롤된 `numbers`박스가 바로 화면에 표시되는 것을 확인할 수 있다.
	- 왜냐하면, `useLayoutEffect`는 **화면이 업데이트되기 전, 동기적으로 effect를 실행시키기 때문**에, 화면이 업데이트되기 전에 **UI 배치를 계산할 수 있다는 것**을 보장하기 때문이다.

- 이처럼, **사용자에게 보여지는 UI를 보다 정교하게 사용**하기 위해서는, `useLayoutEffect`를 사용하는 것이 도움이 될 수 있다.
	- 다만, `effect`가 모두 완료되기 전까지는 화면 업데이트가 완료되지 ㅁ