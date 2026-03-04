
- 현대적인 React 애플리케이션은 클라이언트 컴포넌트와 서버 컴포넌트를 조합해 동적인 사용자 경험을 제공한다.
	- 이 과정에서 "Placeholder"라는 개념은 **서버에서 클라이언트 컴포넌트를 처리**하고 **Hydration(재활성화)**을 준비하는 데 중요한 역할을 한다.

---
### 1. Placeholder란?

- Placeholder는 서버에서 렌더링된 HTML 내에 **클라이언트 컴포넌트가 자리 잡을 공간**을 미리 만들어두는 마커이다.  
- 이 마커는 브라우저에서 JavaScript가 로드된 후 해당 클라이언트 컴포넌트를 동작 가능하게 재활성화(Hydration)하기 위해 사용된다.

---
### 2. Placeholder의 필요성

서버에서 클라이언트 컴포넌트를 렌더링할 때, 다음과 같은 이유로 Placeholder가 필요하다.

##### 1. **클라이언트 컴포넌트의 동작 방식**
- 클라이언트 컴포넌트는 브라우저에서 JavaScript를 통해 이벤트 처리, 상태 관리 등을 활성화한다.
- 서버는 클라이언트 컴포넌트를 완전히 렌더링할 수 없으며, 단순히 자리만 만들어둔 상태로 브라우저에 전달한다.
	- 이 "자리 비우기"는 이후 Hydration 과정을 통해 컴포넌트를 동작 가능하게 만든다.

##### 2. Hydration 과정 준비
- Hydration이란, 서버에서 생성된 정적인 HTML을 브라우저가 가져와 자바스크립트와 결합하여, 동적으로 동작할 수 있도록 만드는 과정이다.

- Placeholder는 다음과 같은 과정을 지원한다:
	1. 서버에서 클라이언트 컴포넌트의 HTML 구조를 미리 만들어 제공
	2. 브라우저에서 JavaScript가 로드되면, 이 구조를 기반으로 컴포넌트를 활성화

##### 3. **서버와 클라이언트의 역할 분리**
- 서버는 초기 HTML 구조를 빠르게 생성해 사용자에게 전달한다.
- 클라이언트는 동적 기능(예: 버튼 클릭, 폼 입력 등)을 자바스크립트를 통해 처리한다.
	- 이 역할 분리를 통해 서버는 성능을 최적화하고 클라이언트는 동적 UI를 제공할 수 있게 된다.

---
### 4. Placeholder의 동작 예시

- React 서버 컴포넌트와 클라이언트 컴포넌트의 조합에서 Placeholder가 어떻게 동작하는지 살펴보자.

##### 1. React 컴포넌트 예시
```jsx
import ServerComponent from './ServerComponent';
import ClientComponent from './ClientComponent';

export default function App() {
  return (
    <div>
      <ServerComponent />
      <ClientComponent />
    </div>
  );
}
```
- `<App/>`에서 서버 컴포넌트와 클라이언트 컴포넌트를 렌더링하는 상황을 가정하자.

##### 2. 서버에서 렌더링된 HTML
```jsx
<div>
  <div>서버에서 렌더링된 콘텐츠</div>  <!-- 서버 컴포넌트의 콘텐츠가 여기 완전히 렌더링됨 -->
  <div><!-- Placeholder 자리 --></div>
</div>
```
- `<ServerComponent />`는 서버에서 HTML로 완전히 렌더링된다.
- `<ClientComponent />`는 Placeholder로 자리를 남겨둔다.

##### 3. 브라우저에서 Hydration 완료 후
```jsx
<div>
  <div>서버에서 렌더링된 콘텐츠</div>
  <div>
    <button>클릭</button> <!-- 클라이언트 컴포넌트가 활성화됨 -->
  </div>
</div>

```
- 클라이언트에서 자바스크립트가 로드된 후, Placeholder가 채워지고 이벤트 처리가 활성화된다.

---

### 4. Placeholder의 장점

###### 1. **빠른 초기 렌더링**
- 클라이언트 컴포넌트의 동작 여부와 상관없이 서버에서 데이터를 포함하여 초기 HTML 구조를 만든 다음 클라이언트에게 전달하기 때문에, 사용자 입장에서 빠르게 UI를 확인할 수 있다.

###### 2. **점진적 Hydration**
- 클라이언트 컴포넌트가 자바스크립트와 함께 로드되면 필요한 부분만 점진적으로 활성화할 수 있다.

###### 3. **서버 리소스 최적화**
- 서버는 클라이언트 컴포넌트의 동작(예: 이벤트 처리)을 책임지지 않아 성능을 최적화할 수 있다.

---
### 5. Placeholder를 활용한 성능 최적화

- 서버 컴포넌트와 클라이언트 컴포넌트를 조합할 때, Placeholder를 적절히 활용하면 성능을 극대화할 수 있다:
	1. 서버에서 렌더링 가능한 부분은 최대한 서버 컴포넌트로 처리.
	2. 동적 로직이 필요한 부분만 클라이언트 컴포넌트로 분리.
	3. 클라이언트 컴포넌트는 필요 시점에 Hydration으로 활성화.

---
### 6. 점진적 Hydration 예시

- 서버 컴포넌트와 클라이언트 컴포넌트가 아래와 같이 구성되어 있을 때를 예시로 들어보자.

> 서버 컴포넌트 : `Page.tsx`
```tsx
// Page.tsx (서버 컴포넌트)
import ImageComponent from "./ImageComponent";

export default function Page() {
  return (
    <div>
      <h1>Welcome to Our Page</h1>
      <p>This is a page with an image that loads progressively.</p>
      <ImageComponent
        src="/hero-mobile.png"
        alt="Content Mobile Preview Image"
      />
    </div>
  );
}
```

> 클라이언트 컴포넌트 : `ImageComponent.tsx` 
```jsx
"use client";

import React, { useEffect, useState } from "react";

/**
 * @name ImageCompProps
 * @description 이미지 컴포넌트의 props
 *
 * @param src
 * @description 이미지 경로
 *
 * @param alt
 * @description 이미지 대체 텍스트
 */
interface ImageCompProps {
  src: string;
  alt: string;
}
export default function ImageComponent({ src, alt }: ImageCompProps) {
  const [isLoaded, setIsLoaded] = useState(false);

  useEffect(() => {
    const img = new Image();
    img.src = src;
    img.onload = () => setIsLoaded(true);
  }, [src]);

  return (
    <div>
      {isLoaded ? (
        <img src={src} alt={alt} />
      ) : (
        <div>Loading image...</div> // placeholder
      )}
    </div>
  );
}
```

###### 1. **서버에서 렌더링된 HTML (초기)**
- 서버에서 HTML이 렌더링될 때, `ImageComponent`는 **placeholder**로 이미지를 표시하며, 실제 이미지는 클라이언트에서 로드될 때까지 기다린다.
```tsx
<div>
  <h1>Welcome to Our Page</h1>
  <p>This is a page with an image that loads progressively.</p>
  <div>Loading image...</div> <!-- Placeholder for the image -->
</div>
```

- 여기서, 클라이언트 측에서 자바스크립트가 로드되고 실행되지 않았는데 `<div>Loading image...</div>`가 출력되는 이유에 대해 궁금할 수 있다. 이게 가능한 이유는 서버에서 초기 HTML을 생성하되, 데이터를 기반으로 한 HTML을 생성하기 때문이다.
	- React가 서버에서 이 컴포넌트를 렌더링하면, 상태값 `isLoaded`의 초기값을 기준으로 HTML을 생성한다. 예제의 경우 `isLoaded`의 초기값은 `false`이었기 때문에, 따라서 서버는 아래와 같이 placeholder를 포함한 정적 HTML을 생성하는 것이다.
	- 즉, 서버는 자바스크립트를 실행하지 않고 **React 컴포넌트의 초기 상태를 사용해 HTML을 생성하므로 `isLoaded`가 `false`로 렌더링된 상태의 결과만 전송된 것**이다.

###### 2. **클라이언트에서 Hydration 시작**
- 클라이언트에서 React가 자바스크립트를 실행하면, `ImageComponent`는 `useEffect`를 통해 이미지를 비동기적으로 로드하기 시작한다. 
	- `useEffect`는 자바스크립트가 클라이언트에서 로드된 후에만 실행된다.
	- 브라우저가 React 자바스크립트 번들을 로드하고, React `useEffect` hook이 실행되면 이미지를 로드하기 시작한다.
```tsx
const img = new Image();
img.src = "/hero-mobile.png";
img.onload = () => setIsLoaded(true);
```

- 이미지가 로드되면 `isLoaded` 상태가 `true`로 변경되고, `placeholder`가 실제 이미지로 교체된다.
```tsx
<div>
  <h1>Welcome to Our Page</h1>
  <p>This is a page with an image that loads progressively.</p>
  <img src="/hero-mobile.png" alt="Content Mobile Preview Image" />
</div>
```

- 이렇게 하면, placeholder를 표시한 상태에서 실제 이미지를 비동기적으로 로드하는 점진적 로딩이 가능해진다.

###### 3. placeholder 동작
- **서버 렌더링**: `ImageComponent`는 서버에서 `placeholder`로 `Loading image...`라는 텍스트를 표시한다.
- **클라이언트에서 점진적 로딩**: 자바스크립트가 클라이언트 측에서 로드되고 실행되면서 React의 `useEffect` hook이 실행된다. 이 `useEffect`를 통해 이미지를 비동기적으로 로드하고, 로드 완료 후 `isLoaded` 상태를 `true`로 변경하여 실제 이미지를 렌더링한다.
- **점진적 활성화**: 이미지를 비동기적으로 로드하여, 사용자 경험을 방해하지 않고 페이지를 점진적으로 활성화한다.

---
### 7. 결론

- Placeholder는 React와 같은 현대적인 프레임워크에서 서버와 클라이언트 간 역할 분리를 통해 성능을 최적화하고, 사용자 경험을 향상시키는 중요한 개념이다.
	- 이를 통해 서버는 빠르게 HTML을 제공하고, 클라이언트는 동적으로 동작하는 UI를 완성할 수 있다.

- Placeholder를 올바르게 이해하고 활용하면, 복잡한 웹 애플리케이션에서도 최적의 성능과 사용자 경험을 제공할 수 있다.