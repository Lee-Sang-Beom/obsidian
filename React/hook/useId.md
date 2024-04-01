
#### 1. useId란?

- `useId hook`은 React 18에 추가된 `hook`으로, 유일한 `id`를 만드는 데 사용된다.
- 아래와 같이 사용하면, 랜덤으로 `id`가 반환된다.
```tsx
const id = useId();
console.log("id", id); // :Rammmkq:
```


#### 2. 예제 1. 

- 다음과 같이 접근성을 고려한 `id`부여에 용이하다.
```tsx
"use client";

import { useId } from "react";
export default function Component() {
  const id = useId();

  function MyInput() {
    return (
      <div>
        <label htmlFor={`${id}`}>이름</label>
        <input id={`${id}`} />
      </div>
    );
  }

  return (
    <div>
      <MyInput />
    </div>
  );
}
```

- 하지만, 이 방법은 여러 개의 컴포넌트가 반환되는 경우 `id`가 겹치게 되는 문제가 발생한다.


#### 3. 예제 1. 문제해결

- `MyInput` 컴포넌트 안에서 `useId hook`을 호출하면 어떻게 될까?
	- 각 컴포넌트마다 고유한 `id`가 생성되어 `input`에 들어가게 된다.
```tsx
"use client";
import { useId } from "react";
export default function Component() {
  function MyInput() {
    const id = useId();

    return (
      <div>
        <label htmlFor={`${id}`}>이름</label>
        <input id={`${id}`} />
      </div>
    );
  }

  return (
    <div>
      <MyInput />
      <MyInput />
      <MyInput />
    </div>
  );
}

```
![[useId(1).png]]