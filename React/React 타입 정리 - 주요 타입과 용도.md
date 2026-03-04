
#### 1. Overview

- 본 내용은 React 프로젝트에서 자주 사용되는 타입들을 정리하여, 각 타입의 역할과 사용 사례를 명확히 이해할 수 있도록 정리한 내용이다.

| **타입**                                    | **설명**                                                          | **주요 용도**                                                         |
| ----------------------------------------- | --------------------------------------------------------------- | ----------------------------------------------------------------- |
| `React.ReactNode`                         | 렌더링 가능한 모든 값. 문자열, JSX, 배열, `null`, `undefined` 등.              | 일반적인 `children` 타입으로 사용.                                          |
| `React.ReactElement`                      | 하나의 React JSX 요소. `type`, `props`, `key` 등의 속성을 가진 React 요소 객체. | 특정 JSX 요소의 타입 지정에 사용.                                             |
| `JSX.Element`                             | JSX 요소의 타입. React 프로젝트에서 JSX를 사용할 때 주로 등장.                      | 단일 JSX 타입을 명확히 지정할 때 사용.                                          |
| `React.ComponentType`                     | 함수형 컴포넌트 또는 클래스형 컴포넌트.                                          | 컴포넌트를 prop으로 전달하거나 HOC에서 사용.                                      |
| `React.FunctionComponent` (또는 `React.FC`) | 함수형 컴포넌트의 타입.                                                   | props 타입과 함께 함수형 컴포넌트를 정의할 때 사용.                                  |
| `React.ReactFragment`                     | 여러 자식 요소를 그룹화하는 React Fragment.                                 | `<>...</>` 또는 `<React.Fragment>...</React.Fragment>`를 명확히 타입화할 때. |
| `React.ElementType`                       | React 컴포넌트 또는 HTML 태그(`"div"`, `"span"`) 등의 타입.                 | 컴포넌트나 태그 이름을 동적으로 처리할 때 사용.                                       |

#### 2. 각 타입의 주요 사용 사례

##### 2-1. **React.ReactNode**
- `children`과 같이 **다양한 타입**의 렌더링 가능한 값을 처리해야 할 때 사용된다.
```tsx
interface Props {
  children: React.ReactNode;
}
const Wrapper: React.FC<Props> = ({ children }) => <div>{children}</div>;

```

##### 2-2. **React.ReactElement**
- **특정 JSX 요소만** 허용하는 경우에 사용된다.
```tsx
interface Props {
  element: React.ReactElement;
}
const SpecificElement: React.FC<Props> = ({ element }) => <div>{element}</div>;
```

##### 2-3. **JSX.Element**
- JSX 문법으로 생성된 요소의 타입을 직접 명시할 때 사용된다.
```tsx
const Example: JSX.Element = <div>Hello World</div>;
```

##### 2-4. **React.ComponentType**
- 컴포넌트를 동적으로 전달하거나 HOC에서 활용된다.
```tsx
interface Props {
  Component: React.ComponentType;
}
const DynamicComponent: React.FC<Props> = ({ Component }) => <Component />;

```

##### 2-5.  **React.FunctionComponent** (`React.FC`)
- 함수형 컴포넌트의 타입을 정의하면서 사용할 수 있으며, props를 명시할 수 있다.
```tsx
interface Props {
  title: string;
}
const Header: React.FC<Props> = ({ title }) => <h1>{title}</h1>;
```

##### 2-6. **React.ReactFragment**
- React Fragment를 명확히 타입화할 때 사용한다.
```tsx
const FragmentExample: React.ReactFragment = (
  <>
    <p>First line</p>
    <p>Second line</p>
  </>
);
```

##### 2-7. **React.ElementType**
- 컴포넌트나, HTML 태그를 동적으로 처리할 때 유용하다.
```tsx
interface Props {
  as: React.ElementType;
}
const DynamicTag: React.FC<Props> = ({ as: Tag }) => <Tag>Hello</Tag>;

```