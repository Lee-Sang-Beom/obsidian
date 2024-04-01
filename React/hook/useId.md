
#### 1. useId란?

- `useId hook`은 React 18에 추가된 `hook`으로, 유일한 `id`를 만드는 데 사용된다.
- 아래와 같이 사용하면, 랜덤으로 `id`가 반환된다.
```tsx
const id = useId();
console.log("id", id); // :Rammmkq:
```


#### 2. 예제 1. 여러 컴포넌트에 id 부여하기 

- 다음과 같이 접근성을 고려한 `id`부여에 용이하다.
	- 하지만, 이 방법은 여러 개의 컴포넌트가 반환되는 경우 `id`가 겹치게 되는 문제가 발생한다.
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


#### 3. 예제 2. 한 컴포넌트 내에서 여러 `id` 관리

- 그럼, 만약 한 컴포넌트 내에서 여러 `id`를 관리해야한다면 어떻게 해야할까?
	- 먼저, `useId`를 여러 번 호출하는 방법이 있다.
```tsx
"use client";
import { useId } from "react";
export default function Component() {
  function MyInput() {
    const id = useId();
    const id2 = useId();
    const id3 = useId();

    return (
      <div>
        {/* 1 */}
        <label htmlFor={`${id}`}>이름</label>
        <input id={`${id}`} />

        {/* 2 */}
        <label htmlFor={`${id2}`}>이름</label>
        <input id={`${id2}`} />

        {/* 3 */}
        <label htmlFor={`${id3}`}>이름</label>
        <input id={`${id3}`} />
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

- 하지만, 이런 경우 `input`태그가 너무 많으면, `useId`를 너무 많이 호출해야 할 수도 있다.
	- 이는 굉장히 비효율적이다.
	- 그럼 `useId`를 1회만 호출하는 방법은 없을까?

- 정답은 **의미있는 문자열**을 뒤에 연결하는 방법을 사용하는 것이다.
	- 이렇게 하면, `id`를 한 번만 호출할 수 있을 뿐더러, `id`가 어떤 의미를 가진 것으로서 연결되어 있는를 쉽게 확인할 수도 있다.
```tsx
"use client";
import { useId } from "react";
export default function Component() {
  function MyInput() {
    const id = useId();

    return (
      <div>
        {/* 1 */}
        <label htmlFor={`${id}_name`}>이름</label>
        <input id={`${id}_name`} />

        {/* 2 */}
        <label htmlFor={`${id}_phoneNum`}>전화번호</label>
        <input id={`${id}_phoneNum`} />

        {/* 3 */}
        <label htmlFor={`${id}_age`}>나이</label>
        <input id={`${id}_age`} />
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
![[useId(2).png]]


#### 4. `useId` 사용 장점

###### 1. 아이디의 생김새

- `useId`가 만든 `id`는 **항상** `:`를 포함한다.
	- 이는 `document.querySelector(id)`와 같은 형태로 `id`를 불러오지 못하게 만든다. 
		- react에서 `querySelector`를 사용하는 것은 **좋은 방법이 아니다.** 이미 여러가지 경우를 대비해서, **잘 설계된** `ref`라는 기능을 제공하고 있다

- react에서는 특정 `element`에 접근하는 방법으로 이미 **잘 설계된** `useRef`라는 `hook`을 제공하고 있다. 
	- 따라서, `useId`는 의도적으로 `querySelector`의 사용을 막음으로써 react 개발자로서 더 나은 코드를 개발할 수 있도록 돕는다고 볼 수 있다.
	- 약간, **더 좋은 게 있는데, 굳이 위험한 코드를 쓰지 말라는** 의도같다.
		- 실제로, `document`객체는 `useEffect`를 사용하여 DOM이 있을 때만 접근이 가능했기 때문에, 완전 공감이 간다.
```tsx
"use client";
import { useEffect, useId } from "react";
export default function Component() {
  function MyInput() {
    const id = useId();

    useEffect(() => {
      // 불가
      const element = document.querySelector(id);
      console.log("element : ", element);
    }, []);

    return (
      <div>
        <button id="btn">버튼</button>
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

###### 2. 안정성

- 물론 `id`를 `Math.random`같은 랜덤한 값으로 적용해줄 수도 있다.
	- 하지만, 이 방법은 컴포넌트가 렌더링될 때마다 `id`가 새로운 값으로 변경되게 된다.
	- `컴포넌트의 렌더링 -> 컴포넌트 함수 재호출 -> 내부 변수 초기화` 과정을 거치기 때문이다.

- `id`가 계속해서 바뀌면, Form 요소의 `id`도 계속해서 변경되는데, 이는 스크린 리더를 사용하는 사용자가 불편함을 느끼게 한다.

- 반면, `useId`를 사용한 `id`는 **컴포넌트의 렌더링이 발생해도 유지된다는 특징**이 있다.
	- 이 점에서, 안정적이라고 볼 수 있다.
```tsx
"use client";
import { useEffect, useId } from "react";
export default function Component() {
  function MyInput() {
    // 매번 새로운 id 생성 (렌더링 시에도 새로 생성)
    const id = Math.random();

    return (
      <div>
        <button id="btn">버튼</button>
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

###### 3. 서버사이드 렌더링(SSR)과 hydration

- **서버사이드 렌더링(SSR)**이란, 페이지를 서버에서 렌더링한 후 클라이언트로 전달하는 방법을 의미한다. 그리고, 클라이언트에서는 서버에서 전달한 페이지를 상호작용이 가능한 페이지로 렌더링하는데, 이러한 과정을 **hydration**이라고 한다.

- 서버사이드 렌더링(SSR)으로 개발하다보면, 서버에서 렌더링한 **결과물**과 클라이언트에서 렌더링한 **결과물**이 일치하지 않아 문제가 일어나는 경우가 있다.
	- **문제발생 예시: ** 서버사이드 렌더링된 **Form** 요소의 `id` !== 클라이언트 렌더링 **Form** 요소의 `id`

- `useId`를 사용하면 서버와 클라이언트에서 모두 동일한, **안정적인** `id`를 생성해주기 때문에, 위의 문제를 회피할 수 있다.