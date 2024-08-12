
#### 1. Actions

- React 앱에서 데이터 변경을 처리할 때, 전통적으로 `useState`를 사용하여 대기 상태(pending), 에러, 낙관적 업데이트(optimistic updates), 그리고 순차적 요청을 수동으로 처리해야 했습니다. 예를 들어, 사용자가 이름을 변경할 때 API 요청을 보내고, 그에 따른 대기 상태와 에러 등을 직접 관리해야 했습니다.

- React 19에서는 비동기 함수를 트랜지션(transition)에서 사용하여 대기 상태, 에러, 폼 제출, 낙관적 업데이트를 자동으로 처리할 수 있게 되었습니다. 예를 들어, `useTransition` 훅을 사용하여 대기 상태를 자동으로 관리할 수 있습니다.
```tsx
// Using pending state from Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);
  const [isPending, startTransition] = useTransition();

  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name);
      if (error) {
        setError(error);
        return;
      } 
      redirect("/path");
    })
  };

  return (
    <div>
      <input value={name} onChange={(event) => setName(event.target.value)} />
      <button onClick={handleSubmit} disabled={isPending}>
        Update
      </button>
      {error && <p>{error}</p>}
    </div>
  );
}
```


#### 2. `useActionState`

- React 19에서는 **useActionState**라는 새로운 훅이 추가되었습니다. 이 훅은 일반적인 Actions 작업을 더 쉽게 처리할 수 있도록 도와줍니다. `useActionState`는 함수를 받아, 호출할 때마다 대기 상태와 최종 결과를 관리합니다

```tsx
const [error, submitAction, isPending] = useActionState(
  async (previousState, newName) => {
    const error = await updateName(newName);
    if (error) {
      return error;
    }
    return null;
  },
  null,
);

```
- 이 훅은 비동기 함수(“Action”)를 받아 처리하며, 결과와 대기 상태를 반환합니다. 이전에 "ReactDOM.useFormState"로 불리던 이 훅은 이제 **React.useActionState**로 이름이 변경되었습니다.
	- 이러한 새로운 기능들은 React 19에서의 데이터 관리와 상호작용을 더욱 간단하고 효율적으로 만들어줍니다.

#### 3. `<form>` Actions

- React 19에서는 `<form>` 요소에서 `action`과 `formAction` 속성에 함수들을 전달할 수 있게 되어, 자동으로 폼을 제출할 수 있습니다. 폼 제출이 성공하면 React가 비제어 컴포넌트의 폼을 자동으로 리셋합니다.


#### 4. `useFormStatus` Hook

- 디자인 시스템에서 `<form>` 상태 정보를 쉽게 얻기 위해 `useFormStatus`라는 새로운 훅이 추가되었습니다. 이 훅은 폼이 진행 중인지 (`pending`) 여부를 알려주며, 이를 통해 하위 컴포넌트에서 폼 상태를 쉽게 참조할 수 있습니다.
```tsx
import {useFormStatus} from 'react-dom';  

function DesignButton() {  
	const {pending} = useFormStatus();  
	return <button type="submit" disabled={pending} />  
}
```

#### 5. `useOptimistic` Hook

- 비동기 요청 중에 최종 상태를 낙관적으로 미리 보여주는 UI 패턴을 쉽게 구현하기 위해 `useOptimistic` 훅이 추가되었습니다. 이 훅을 사용하면 비동기 요청이 완료되기 전에도 예상되는 결과를 화면에 미리 표시할 수 있습니다.
```tsx
function ChangeName({currentName, onUpdateName}) {
  const [optimisticName, setOptimisticName] = useOptimistic(currentName);

  const submitAction = async formData => {
    const newName = formData.get("name");
    setOptimisticName(newName);
    const updatedName = await updateName(newName);
    onUpdateName(updatedName);
  };

  return (
    <form action={submitAction}>
      <p>Your name is: {optimisticName}</p>
      <p>
        <label>Change Name:</label>
        <input
          type="text"
          name="name"
          disabled={currentName !== optimisticName}
        />
      </p>
    </form>
  );
}
```

#### 6. use API

- React 19에서는 `use` API가 도입되어, 렌더링 중에 Promise를 읽을 수 있습니다. `use`는 Promise가 해결될 때까지 컴포넌트를 일시 중지하고, 조건부로 컨텍스트를 읽을 수 있도록 지원합니다.
```tsx
import {use} from 'react';

function Comments({commentsPromise}) {
  // `use` will suspend until the promise resolves.
  const comments = use(commentsPromise);
  return comments.map(comment => <p key={comment.id}>{comment}</p>);
}

function Page({commentsPromise}) {
  // When `use` suspends in Comments,
  // this Suspense boundary will be shown.
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  )
}
```

- `use` 훅은 렌더링 중에 생성된 프로미스를 지원하지 않습니다. 만약 렌더링 중에 생성된 프로미스를 `use`에 전달하려고 하면, React가 경고를 발생시킵니다.
	- 이 문제를 해결하려면, 프로미스를 캐싱할 수 있는 기능을 지원하는 Suspense 기반 라이브러리나 프레임워크에서 프로미스를 전달해야 합니다. 앞으로 React는 렌더링 중에 프로미스를 더 쉽게 캐싱할 수 있도록 새로운 기능을 제공할 계획입니다.
```tsx
import {use} from 'react';
import ThemeContext from './ThemeContext'

function Heading({children}) {
  if (children == null) {
    return null;
  }
  
  // This would not work with useContext
  // because of the early return.
  const theme = use(ThemeContext);
  return (
    <h1 style={{color: theme.color}}>
      {children}
    </h1>
  );
}
```


#### 7. React Server Components

- 서버 컴포넌트는 클라이언트 애플리케이션이나 SSR 서버와 별도의 환경에서 미리 렌더링될 수 있는 새로운 옵션입니다. React 19에서는 서버 컴포넌트 기능이 안정화되어 사용 가능하며, 일부 API는 향후 변경될 수 있습니다.


#### 8. Server Actions

- 클라이언트 컴포넌트에서 서버에서 실행되는 비동기 함수를 호출할 수 있는 Server Actions 기능이 추가되었습니다. `"use server"` 지시어를 사용하여 서버에서 실행되는 함수의 참조를 클라이언트에 전달할 수 있습니다.


#### 9. Ref as a Prop

- 함수형 컴포넌트에서 `ref`를 속성으로 전달할 수 있게 되었으며, 더 이상 `forwardRef`가 필요하지 않습니다. 이 변경은 리팩토링을 통해 기존 코드에 자동으로 적용할 수 있습니다.
```tsx
function MyInput({placeholder, ref}) {  
	return <input placeholder={placeholder} ref={ref} />  
}  

//...  

<MyInput ref={ref} />
```


#### 10. Hydration Error 개선

- React 19에서는 Hydration 오류를 더 잘 처리하고, 오류 발생 시 더욱 유용한 정보를 제공합니다.


#### 11. `<Context>` as a Provider

- 이제 `<Context>`를 `<Context.Provider>` 대신 사용할 수 있습니다. 기존 코드도 자동으로 업데이트할 수 있는 코드모드를 제공할 예정입니다.
```tsx
const ThemeContext = createContext('');

function App({children}) {
  return (
    <ThemeContext value="dark">
      {children}
    </ThemeContext>
  );  
}
```


#### 12. Ref 콜백에 정리 함수 추가

- `ref` 콜백에서 정리 함수(cleanup function)를 반환할 수 있으며, 컴포넌트가 언마운트될 때 React가 이를 자동으로 호출합니다.
```tsx
<input
  ref={(ref) => {
    // ref created

    // NEW: return a cleanup function to reset
    // the ref when element is removed from DOM.
    return () => {
      // ref cleanup
    };
  }}
/>
```

- 이 때, `ref` 정리 함수가 도입되면서, 이제 `ref` 콜백에서 다른 값을 반환하려고 하면 TypeScript가 이를 거부하게 됩니다. 
	- 이를 해결하려면 암시적 반환을 사용하지 않도록 수정해야 합니다. 예를 들면 아래와 같다.
```tsx
- <div ref={current => (instance = current)} /> // 사용 불가
+ <div ref={current => {instance = current}} /> // 사용 가능
```


#### 13. useDeferredValue 초기 값 옵션

- `useDeferredValue` 훅에 초기 값 옵션이 추가되었습니다. 이 옵션을 사용하면 초기 렌더링에서 기본 값을 사용할 수 있습니다.
```tsx
function Search({deferredValue}) {
  // On initial render the value is ''.
  // Then a re-render is scheduled with the deferredValue.
  const value = useDeferredValue(deferredValue, '');
  
  return (
    <Results query={value} />
  );
}
```


#### 14. 문서 메타데이터 지원

- React 19에서는 `<title>`, `<meta>`, `<link>`와 같은 문서 메타데이터 태그를 컴포넌트 내에서 직접 렌더링할 수 있습니다. 이 태그들은 자동으로 문서의 `<head>` 섹션으로 이동됩니다.
```tsx
function BlogPost({post}) {
  return (
    <article>
      <h1>{post.title}</h1>
      <title>{post.title}</title>
      <meta name="author" content="Josh" />
      <link rel="author" href="https://twitter.com/joshcstory/" />
      <meta name="keywords" content={post.keywords} />
      <p>
        Eee equals em-see-squared...
      </p>
    </article>
  );
}
```


#### 15. 스타일시트 지원

- React 19는 컴포넌트 내에서 스타일시트(`<link rel="stylesheet">` 및 `<style>`)를 로드하고 관리하는 기능을 제공합니다. 이를 통해 스타일 로딩의 복잡성을 줄일 수 있습니다.

- 스타일시트는 외부에서 링크된 스타일시트(`<link rel="stylesheet" href="...">`)와 인라인 스타일시트(`<style>...</style>`) 두 가지가 있습니다. 
	- 스타일 규칙의 우선순위 때문에 DOM 내에서 스타일시트의 위치가 매우 중요합니다.
	- 컴포넌트 내에서 스타일시트를 조합하는 것은 어려운 작업이기 때문에, 대부분의 사용자는 모든 스타일을 특정 컴포넌트와 멀리 떨어진 곳에서 로드하거나, 이 복잡성을 처리하는 스타일 라이브러리를 사용합니다.

- React 19에서는 이러한 복잡성을 줄이기 위해 클라이언트 측의 동시 렌더링(Concurrent Rendering)과 서버 측의 스트리밍 렌더링(Streaming Rendering)에서 스타일시트를 위한 통합 지원을 제공합니다.
	- React에 스타일시트의 우선순위를 알려주면, React는 DOM에서 스타일시트의 삽입 순서를 관리하고, 외부 스타일시트의 경우 해당 스타일시트가 로드된 후에만 해당 스타일 규칙에 의존하는 콘텐츠를 표시합니다.

```tsx
function ComponentOne() {
  return (
    <Suspense fallback="loading...">
      <link rel="stylesheet" href="foo" precedence="default" />
      <link rel="stylesheet" href="bar" precedence="high" /> {/* 먼저 로드 */}
      <article class="foo-class bar-class">
        {...}
      </article>
    </Suspense>
  )
}

function ComponentTwo() {
  return (
    <div>
      <p>{...}</p>
      <link rel="stylesheet" href="baz" precedence="default" />  <-- will be inserted between foo & bar
    </div>
  )
}
```
- `ComponentOne`에서는 두 개의 스타일시트를 로드하며, 각 스타일시트는 우선순위(`precedence`)가 지정됩니다.
- `ComponentTwo`에서 로드되는 스타일시트는 `ComponentOne`에서 로드된 `foo`와 `bar` 스타일시트 사이에 삽입됩니다.
	- `ComponentOne` 로드됨 -> `ComponentTwo` 로드됨
	- 여기서, 스타일 시트는 로드 순서에 따라 `"foo" -> "baz" -> "bar"`가 된다.


#### 16. 비동기 스크립트 지원

- 비동기 스크립트(`<script async>`)를 컴포넌트 내 어디에서든 렌더링할 수 있으며, React가 중복된 스크립트를 방지해 줍니다.
```tsx
function MyComponent() {
  return (
    <div>
      <script async={true} src="..." />
      Hello World
    </div>
  )
}

function App() {
  <html>
    <body>
      <MyComponent>
      ...
      <MyComponent> // won't lead to duplicate script in the DOM
    </body>
  </html>
}
```


#### 17. 자원 사전 로딩 지원

- React 19는 자원 프리페치(pfrefetch), 프리로드(preload) 및 프리커넥트(preconnect)를 위한 새로운 API를 추가했습니다. 이로 인해 초기 페이지 로드 및 클라이언트 업데이트 성능이 향상됩니다.
```js
import { prefetchDNS, preconnect, preload, preinit } from 'react-dom'
function MyComponent() {
  preinit('https://.../path/to/some/script.js', {as: 'script' }) // loads and executes this script eagerly
  preload('https://.../path/to/font.woff', { as: 'font' }) // preloads this font
  preload('https://.../path/to/stylesheet.css', { as: 'style' }) // preloads this stylesheet
  prefetchDNS('https://...') // when you may not actually request anything from this host
  preconnect('https://...') // when you will request something but aren't sure what
}
```

```html

<!-- the above would result in the following DOM/HTML -->
<html>
  <head>
    <!-- links/scripts are prioritized by their utility to early loading, not call order -->
    <link rel="prefetch-dns" href="https://...">
    <link rel="preconnect" href="https://...">
    <link rel="preload" as="font" href="https://.../path/to/font.woff">
    <link rel="preload" as="style" href="https://.../path/to/stylesheet.css">
    <script async="" src="https://.../path/to/some/script.js"></script>
  </head>
  <body>
    ...
  </body>
</html>
```


#### 18. 타사 스크립트 및 확장 프로그램과의 호환성

- 타사 스크립트 및 브라우저 확장 프로그램이 추가한 예기치 않은 요소들로 인한 Hydration 오류가 발생하지 않도록 개선되었습니다.


#### 19. 향상된 오류 보고

- React 19에서는 오류 보고가 개선되어 중복된 오류를 제거하고, 모든 관련 정보를 포함한 단일 오류 메시지를 제공합니다.


#### 20. 사용자 정의 요소 지원

- React 19는 사용자 정의 요소(Custom Elements)에 대한 완전한 지원을 추가했습니다. 서버 사이드 렌더링 시, 기본 데이터 유형은 속성으로 렌더링되고, 비기본 유형은 생략됩니다.