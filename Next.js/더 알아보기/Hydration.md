
### 1. Hydration이란?

- **Hydration**은 React와 같은 프레임워크에서 **서버에서 미리 렌더링된 HTML을 클라이언트에서 동적으로 연결**하여 React의 상태 관리와 이벤트 처리가 활성화되도록 하는 과정을 말한다. 
- 이를 통해 SSR(Server-Side Rendering)으로 생성된 정적 HTML이 클라이언트에서 React 앱으로 변환되어 동적인 UI와 상호작용을 지원할 수 있게 된다.

---
### 2. Hydration의 핵심 동작

1. **SSR로 정적 HTML 생성**:
    - 서버에서 React 컴포넌트가 렌더링되어 정적인 HTML로 변환된다.
    - 이 HTML은 브라우저에 전달되며, 사용자가 페이지를 로드하면 초기 화면이 즉시 표시된다. 이를 통해 첫 화면 로딩 속도를 높일 수 있다.

2. **클라이언트로 HTML 전달**:
    - 브라우저는 서버에서 전달된 HTML을 DOM으로 렌더링하여 사용자에게 표시한다.
    - 이 시점의 HTML은 정적이며, 동적 상호작용(클릭, 입력, 상태 변경 등)은 아직 활성화되지 않은 상태이다.

3. **React 앱 초기화**:
    - 클라이언트에서 React의 JavaScript 번들이 로드된다.
    - React는 서버에서 렌더링된 HTML과 클라이언트의 React 컴포넌트 구조를 비교한다.
    - 이 과정을 통해 기존의 HTML DOM에 React의 이벤트 처리 및 상태 관리를 연결한다.

4. **이벤트 및 상태 활성화**:
    - 연결이 완료되면 React는 DOM을 제어하고, 사용자의 입력 및 상호작용에 반응할 수 있다.
    - 이 과정을 "Hydration"이라고 부른다.

---
### 3. Hydration의 필요성

- **초기 로딩 속도 개선**:
    - SSR을 통해 초기 HTML이 생성되므로, 사용자는 브라우저에서 빠르게 페이지를 볼 수 있다.
    - 이후 Hydration이 완료되면 동적인 상호작용이 가능해진다.

- **SEO(검색 엔진 최적화)**:
    - 검색 엔진은 HTML 콘텐츠를 크롤링하는데, 이 때 서버에서 렌더링된 HTML이 제공되면 SEO 성능이 향상된다.

- **React 생태계 통합**:
    - 클라이언트에서 React가 서버 렌더링된 HTML과 연결되므로 서버와 클라이언트 사이의 통합이 원활하게 이루어진다.

---
### 4. Hydration 동작 예시

> 서버 컴포넌트와 클라이언트 컴포넌트가 함께 동작하는 구조
```jsx
function ServerComponent() {
  return <div>서버에서 렌더링된 내용입니다.</div>;
}

function ClientComponent({ children }) {
  return <div onClick={() => alert('클릭 이벤트 활성화됨!')}>{children}</div>;
}

function App() {
  return (
    <ClientComponent>
      <ServerComponent />
    </ClientComponent>
  );
}
```