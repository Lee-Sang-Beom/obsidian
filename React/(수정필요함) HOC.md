
### 📚 HOC란 무엇인가?

**Higher-Order Component (HOC)** 는 React에서 *컴포넌트를 인수로 받아 새로운 컴포넌트를 반환*하는 **함수**이다.  
- 이는 컴포넌트 로직을 재사용할 수 있도록 설계된 고급 기술이다.

> **HOC의 정의:**  
> `컴포넌트를 입력으로 받아 새로운 컴포넌트를 반환하는 함수`

---
### 🤔 왜 HOC를 사용하는가?

- React에서는 컴포넌트 간에 로직을 공유하기 위해 주로 다음과 같은 패턴을 사용한다
	1. **Props를 통한 데이터 전달**
	2. **Render Props**
	3. **HOC**

- HOC는 특히 **공통 로직을 재사용**하거나, **컴포넌트를 확장**하는 데 유용하다.

---
### 📌 주요 사용 사례

- **권한 검사**  
    특정 조건에서 컴포넌트를 렌더링하거나 접근 제한.

- **로깅 및 디버깅**  
    컴포넌트의 상태나 props를 추적.

- **데이터 Fetching**  
    API 데이터를 가져와 하위 컴포넌트에 주입.

---
### 🚀 HOC 사용법
```jsx
// HOC 정의
const withLogger = (WrappedComponent) => {
  return (props) => {
    console.log("Props:", props);
    return <WrappedComponent {...props} />;
  };
};

// 사용 예시
const MyComponent = (props) => {
  return <div>Hello, {props.name}!</div>;
};

const MyComponentWithLogger = withLogger(MyComponent);

// 렌더링
<MyComponentWithLogger name="React" />;
```
- `MyComponentWithLogger`가 렌더링될 때 `props`가 콘솔에 출력된다.
- `MyComponent`는 props를 그대로 받아 UI를 렌더링한다.

---
### 🛠️ HOC 작성 시 주의할 점

##### 1. props 전달 유지 (Props Proxy)
- HOC는 항상 원래 컴포넌트가 필요로 하는 props를 전달해야 합니다.
```jsx
const withExtraProp = (WrappedComponent) => (props) => {
  return <WrappedComponent {...props} extra="추가 속성" />;
};
```

##### 2. Display Name 설정
- 디버깅 시 HOC로 감싸진 컴포넌트의 이름을 알기 쉽도록 `displayName`을 설정합니다.
```jsx
const withLogger = (WrappedComponent) => {
  const HOC = (props) => {
    console.log("Props:", props);
    return <WrappedComponent {...props} />;
  };

  HOC.displayName = `withLogger(${WrappedComponent.displayName || WrappedComponent.name || "Component"})`;
  return HOC;
};
```

##### 3. HOC 중첩 관리
- HOC를 여러 개 중첩해서 사용하는 경우, 코드 가독성을 유지하기 위해 **Compose 함수**를 사용하는 것이 좋습니다.
```jsx
import { compose } from "redux"; // 또는 lodash

const enhance = compose(withLogger, withExtraProp);
const EnhancedComponent = enhance(MyComponent);
```

---
### 🎨 HOC의 장단점

##### ✅ 장점
- 컴포넌트 로직 재사용이 용이
- 관심사 분리가 가능
- 기존 컴포넌트의 수정 없이 기능 추가 가능

##### ❌ 단점
- HOC 중첩 시 코드 가독성 저하
- props 충돌 가능성
- React DevTools에서 구조가 복잡하게 표시될 수 있음

---
### 🌟 HOC vs 다른 패턴 비교

|패턴|특징|주요 용도|
|---|---|---|
|**HOC**|컴포넌트를 함수로 감싸 확장|로직 재사용, 상태 관리|
|**Render Props**|children을 함수로 전달받아 로직을 주입|동적인 데이터 렌더링|
|**Custom Hook**|훅을 통해 상태와 로직을 공유|상태/로직 분리, 재사용|

---
### 🔑 요약

- **HOC는 함수**이며, 컴포넌트를 받아 새로운 컴포넌트를 반환하는 역할을 수행한다.
- 공통 로직을 재사용하고, 관심사를 분리하는 데 유용하다.
- 하지만 중첩 사용 시 코드 복잡도가 높아질 수 있으므로 필요에 따라 다른 패턴과 조합해 사용해야 한다.

> **🌟 React 개발에서는 HOC와 Custom Hook을 적절히 활용하여 효율적인 코드베이스를 유지하는 것이 중요하다!**