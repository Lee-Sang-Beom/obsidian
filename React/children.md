
### 1. **`children`이란?**

- **`children`** 은 **React 컴포넌트**에서 **동적으로 전달되는 자식 요소**를 의미한다. 이는 컴포넌트의 **구성 요소로서** 다른 컴포넌트를 포함할 수 있게 해준다.
- **`children`** 은 부모 컴포넌트가 자식 컴포넌트를 **임의로** 받을 수 있도록 설계된 특수한 프로퍼티이다. 부모 컴포넌트는 이 프로퍼티를 통해 자식 컴포넌트를 **동적으로** 렌더링할 수 있게 된다.

### 2. **`children`의 역할**

- 부모 컴포넌트에서 `children`을 사용하면 자식 컴포넌트를 **동적으로 렌더링**할 수 있다. 이는 **다양한 구조와 컴포넌트를 동적으로 조합**할 수 있게 도와준다.
- `children`은 React의 **렌더링 과정에서 `placeholder`로 처리**되며, 전달된 컴포넌트는 **실제 렌더링 시점에서** 동적으로 표시된다.
	- 그리고 동적으로 표시되는 과정에서 **hydration** 과정이 발생한다.
	- **`hydration`** 은 서버에서 **정적인 HTML**을 렌더링하여 클라이언트로 전달한 후, 클라이언트에서 React가 이 HTML을 **상호작용 가능한 React 애플리케이션으로 변환**하는 과정을 의미한다.
		- 즉, 서버에서 제공된 HTML 구조가 **React의 상태와 이벤트를 처리하는 방식으로** 동적 변환되며, **클라이언트에서 동적 상호작용**이 가능해지도록 하는 핵심 기술이다.

### 3. **서버 컴포넌트와 클라이언트 컴포넌트의 역할 구분**

- **서버 컴포넌트**(Server Components, RSC)는 **서버에서 렌더링되어 HTML을 반환**한다. 상호작용을 처리하지 않으며, 서버 측에서 HTML만 생성한다.
- **클라이언트 컴포넌트**(Client Components, RCC)는 **클라이언트에서 동적으로 렌더링**되며, **상호작용**을 처리할 수 있다. React의 이벤트 처리나 상태 관리와 같은 동적 기능을 담당한다.

### 4. **서버 컴포넌트를 `children`으로 전달할 때**

- 부모 컴포넌트가 **클라이언트 컴포넌트**이고, 자식 컴포넌트로 **서버 컴포넌트**를 `children`으로 전달하면, 서버 컴포넌트는 **서버에서 렌더링된 HTML**을 클라이언트로 전달하고, **클라이언트에서 hydration**(동적 처리)을 통해 **클라이언트의 상호작용**을 추가할 수 있다.
- 이 방식은 **서버 컴포넌트와 클라이언트 컴포넌트의 역할을 구분**하여, 서버에서 처리되는 정적 HTML과 클라이언트에서 처리되는 동적 상호작용을 분리할 수 있다.

### 5. **서버 컴포넌트를 직접 클라이언트 컴포넌트에 넣을 때의 문제**

- **클라이언트 컴포넌트** 안에 **서버 컴포넌트를 직접 포함**하려고 하면 문제가 발생한다. 그 이유는 **서버 컴포넌트**는 **상호작용을 처리하지 않기** 때문에, **동적 처리**를 위한 클라이언트 컴포넌트 내에서 사용하기에 적합하지 않기 때문이다.
- React는 **서버 컴포넌트를 클라이언트에서 직접 렌더링**할 수 없다고 간주하여, 이 경우 **에러**를 발생시킨다.

### 6. **`children`을 통한 서버 컴포넌트 전달 시 동작 원리**

- `children`을 통해 서버 컴포넌트를 전달할 경우, 서버에서 HTML만 렌더링된 상태로 전달되고, 클라이언트에서 **hydration**이 진행된다. 이 과정에서 hydrtaion 덕분에 클라이언트에서 상호작용을 처리할 수 있는 코드로 서버컴포넌트가 감싸져 동적으로 처리될 수 있게 된다.
- **`children`을 사용하는 방식**은 **서버 컴포넌트와 클라이언트 컴포넌트** 간의 **경계를 유연하게 처리**할 수 있는 방법으로 적용된다.

### 7. 예시 코드
```jsx
// 서버 컴포넌트
function ChildServerComponent() {
  return <div>server component</div>;
}

// 클라이언트 컴포넌트 (children을 받음)
function ParentClientComponent({ children }) {
  return <div>{children}</div>;
}

// 부모 컴포넌트에서 서버 컴포넌트를 children으로 전달
function ContainerServerComponent() {
  return (
    <ParentClientComponent>
      <ChildServerComponent />
    </ParentClientComponent>
  );
}
```

### 8. **직접 서버 컴포넌트를 클라이언트 컴포넌트에 포함할 때 발생하는 문제**
```jsx
// 서버 컴포넌트
function ChildServerComponent() {
  return <div>server component</div>;
}

// 클라이언트 컴포넌트 (children을 받음)
function ParentClientComponent({ children }) {
  return <div>{children}</div>;
}

// 부모 컴포넌트에서 서버 컴포넌트를 children으로 전달
function ContainerServerComponent() {
  return (
    <ParentClientComponent>
      <ChildServerComponent />
    </ParentClientComponent>
  );
}
```
- 위 코드에서 **`ChildServerComponent`를 `ParentClientComponent` 내에 직접 포함**하려고 하면, React는 이 조합을 **동적으로 처리할 수 없기 때문에 에러**를 발생시킨다.
- 이는 서버 컴포넌트가 클라이언트에서 상호작용을 처리할 수 없으며, `children`이 사용되지 않아 화면 렌더링 시 동적으로 서버컴포넌트가 표시되지 않고, 때문에 hydration이 발생하지 않아 에러가 발생하는 것이다.

### 9. **결론**

- **`children`을 통해 서버 컴포넌트를 전달하는 방식**은 서버와 클라이언트 컴포넌트 간의 역할을 **유연하게 처리**할 수 있도록 돕는다. 서버는 정적 HTML을 반환하고, 클라이언트는 이를 받아서 **동적 상호작용을 추가**하는 방식으로 작동한다.
- 그러나 **서버 컴포넌트를 직접 클라이언트 컴포넌트에 포함**하려고 하면 동적 처리의 역할 구분이 불가능하므로 에러가 발생한다.

