https://react.dev/blog/2024/12/05/react-19

### 1. Actions

- React 앱에서 데이터 변경을 처리할 때, 전통적으로 `useState`를 사용하여 대기 상태(pending), 에러, 낙관적 업데이트(optimistic updates), 그리고 순차적 요청을 수동으로 처리해야 했습니다. 예를 들어, 사용자가 이름을 변경할 때 API 요청을 보내고, 그에 따른 대기 상태와 에러 등을 직접 관리해야 했습니다.

- React 19에서는 비동기 함수를 트랜지션(transition)에서 사용하여 대기 상태, 에러, 폼 제출, 낙관적 업데이트를 자동으로 처리할 수 있게 되었습니다. 예를 들어, `useTransition` 훅을 사용하여 대기 상태를 자동으로 관리할 수 있습니다.
	- React의 `useTransition` 훅은 비동기 작업이 진행되는 동안 UI의 응답성을 유지하는 데 사용된다.
	- `startTransition` 함수는 비동기 작업을 감싸고, 작업이 진행 중일 때 `isPending` 상태를 관리합니다.
		- 이 때, `startTransition`은 전달된 콜백 함수에서 비동기 작업을 수행하는 동안 UI의 업데이트가 **우선순위가 낮은** 작업으로 처리되도록 합니다.
		- 이 코드는 `updateName`이라는 비동기 작업을 트랜지션 내에서 실행하며, 이 작업이 완료될 때까지 React는 UI를 블로킹하지 않고 다른 렌더링 작업을 처리할 수 있도록 합니다.
```tsx
// Using pending state from Actions
function UpdateName({}) {
  const [name, setName] = useState("");
  const [error, setError] = useState(null);

  // startTransition: 비동기 작업을 트랜지션 내에서 실행할 수 있게 해주는 함수
  const [isPending, startTransition] = useTransition(); 
  const handleSubmit = () => {
    startTransition(async () => {
      const error = await updateName(name); // 비동기작업
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

---
### 2. `useActionState`

- React 19에서는 **useActionState**라는 새로운 훅이 추가되었습니다. 이 훅은 일반적인 Actions 작업을 더 쉽게 처리할 수 있도록 도와줍니다. `useActionState`는 함수를 받아, 호출할 때마다 대기 상태와 최종 결과를 관리합니다.
- 이전까지는 `ReactDOM.useFormState`라는 이름으로 사용되었습니다. 

```tsx
// Using <form> Actions and useActionState
function ChangeName({ name, setName }) {
  const [error, submitAction, isPending] = useActionState(
    async (previousState, formData) => {
      const error = await updateName(formData.get("name"));
      if (error) {
        return error;
      }
      redirect("/path");
      return null;
    },
    null,
  );

  return (
    <form action={submitAction}>
      <input type="text" name="name" />
      <button type="submit" disabled={isPending}>Update</button>
      {error && <p>{error}</p>}
    </form>
  );
}
```
- 이 훅은 비동기 함수(“Action”)를 받아 처리하며, 결과와 대기 상태를 반환합니다. 이전에 "ReactDOM.useFormState"로 불리던 이 훅은 이제 **React.useActionState**로 이름이 변경되었습니다.
	- 이러한 새로운 기능들은 React 19에서의 데이터 관리와 상호작용을 더욱 간단하고 효율적으로 만들어줍니다.
	- [[6. Server-side validation and error handling]]

- `useActionState` hook의 반환값은 아래와 같습니다.
	- `error`: 현재 상태에서의 오류를 나타냅니다.
	- `submitAction`: 비동기 작업을 수행하는 함수입니다.
	- `isPending`: 비동기 작업이 진행 중인지를 나타내는 boolean 값입니다.

- 그리고, 다음은 코드의 각 부분에 대한 설명입니다.
	1. **`useActionState`**: 비동기 작업의 상태를 관리하는 커스텀 hook입니다.
		- `useActionState`의 첫 번째 인수로는 비동기 작업을 정의하는 함수가 전달됩니다. 
		- 이 함수는 이전 상태와 새로운 값을 인수로 받아 처리합니다.
	  
	2. **비동기 작업 함수**: 이 함수는 `previousState`와 `newName`을 인수로 받아 비동기 작업을 수행합니다. 
		- `updateName(newName)`은 이름을 업데이트하는 함수이며, 오류가 발생하면 오류를 반환하고, 성공하면 `null`을 반환합니다.
	   
	3. **초기 상태**: 두 번째 인수로는 초기 상태가 전달됩니다. 여기서는 `null`을 사용하고 있습니다.

- 비동기 작업 함수에 전달되는 콜백 함수에 대한 설명은 아래와 같습니다.
	1. **`previousState`**
	    - 이 인수는 비동기 작업이 시작되기 전의 이전 상태를 나타냅니다.
	    - 상태 관리를 위해 사용하는 경우가 많으며, 비동기 작업의 결과에 따라 상태를 업데이트할 때 유용합니다.
	    - 예를 들어, 이전 상태에서 어떤 정보가 있었는지 또는 비동기 작업이 수행되기 전에의 상태를 알고 싶을 때 사용합니다.
      
	2. **`formData`**:
	    - 이 인수는 비동기 작업에 필요한 새로운 데이터를 나타냅니다. 이 코드에서 `formData`는 이름을 업데이트하기 위해 사용되는 값입니다.
	    - `updateName(formData.get("name"))` 함수는 `formData`에 있는 `name` 값을 사용하여 이름을 업데이트하려고 시도하며, 이 작업이 성공하거나 실패할 때 상태를 반환합니다.
	    - 이 코드에서는 `submitAction` 함수가 호출될 때 제공되는 새로운 이름 값입니다. 이 값은 `updateName` 함수에 전달되어 이름 업데이트를 시도합니다.

---
### 3. `<form>` Actions

`<form action={actionFunction}>`
- React 19에서는 `<form>`, `<input>`, `<button>` 요소에 `action`과 `formAction` 속성에 함수들을 전달할 수 있도록 하여, 폼을 자동으로 제출할 수 있도록 하였습니다.
	- 여기서 `actionFunction`은 폼이 제출될 때 호출되는 함수입니다. 이 기능을 통해, 폼을 제출하는 과정에서 개발자가 명시적으로 `submit`을 호출할 필요 없이, 폼에 설정된 액션을 통해 자동으로 제출될 수 있도록 할 수 있습니다.

- 폼 제출이 성공하면 React는 비제어 컴포넌트의 폼을 자동으로 리셋합니다.

---
### 4. `useFormStatus` Hook

- 디자인 시스템에서, 디자인 컴포넌트가 자신이 속한 `<form>`에 대한 정보에 접근해야 할 경우가 많습니다.
	- 이때, **props를 컴포넌트까지 전달하는 방식**(prop drilling)을 사용하지 않고도 폼 정보에 접근할 수 있습니다.
	- 이 기능은 **Context**를 통해 구현할 수 있지만, 이를 더 쉽게 사용하기 위해 새로운 훅인 `useFormStatus`를 추가했습니다.
```tsx
import {useFormStatus} from 'react-dom';  

function DesignButton() {  
	const {pending} = useFormStatus();  
	return <button type="submit" disabled={pending} />  
}
```

- `useFormStatus`는 현재 컴포넌트가 속한 **부모 `<form>`** 의 상태를 읽을 수 있게 해주는 훅입니다.
- `pending` 상태를 읽어와서, 버튼을 **disabled** 상태로 만들 수 있습니다. 예를 들어, 폼이 제출 중(pending)일 때 버튼을 비활성화할 수 있습니다.

> 작동방식
- `useFormStatus` 훅은 부모 `<form>`을 **Context provider**처럼 취급하여, 해당 폼의 상태를 읽을 수 있게 해줍니다.
- 즉, 폼의 상태를 관리하는 컴포넌트에서 `useFormStatus`를 호출하면, 그 부모 폼의 상태를 자동으로 가져오게 되는 방식입니다.

---
### 5. `useOptimistic` Hook

- 데이터를 변경할 때, 비동기 작업이 진행되는 동안 **최종 상태를 미리 보여주는 UI 패턴**은 사용자 경험을 향상시키는 데 유용합니다. React 19에서는 이 패턴을 더 쉽게 구현할 수 있도록 **`useOptimistic`** 훅을 추가했습니다.
```tsx
function ChangeName({ currentName, onUpdateName }) {
  const [optimisticName, setOptimisticName] = useOptimistic(currentName);

  const submitAction = async (formData) => {
    const newName = formData.get("name");
    setOptimisticName(newName); // 비관적 상태 업데이트

    try {
      const updatedName = await updateName(newName); // 비동기 이름 업데이트
      onUpdateName(updatedName); // 이름 변경 완료 후 상태 갱신
    } catch (error) {
      // 오류 발생 시, optimistic 상태를 원래 상태로 롤백
      setOptimisticName(currentName); // 롤백
      console.error('Name update failed:', error);
    }
  };

  return (
    <form action={submitAction}>
      <p>Your name is: {optimisticName}</p>
      <p>
        <label>Change Name:</label>
        <input
          type="text"
          name="name"
          disabled={currentName !== optimisticName} // 현재 이름이 optimistic 이름과 다르면 입력 불가능
        />
      </p>
    </form>
  );
}
```
- **`useOptimistic(currentName)`**:
    - 이 훅은 `currentName` 을 초기 값으로 받아 `optimisticName` 을 설정합니다.
    - 비동기 요청이 진행되는 동안 `optimisticName`이 즉시 렌더링되어 사용자는 새로운 이름을 미리 볼 수 있습니다.

- **`submitAction`**:
    - 사용자가 이름을 변경하면 **새 이름**을 `setOptimisticName`으로 설정하여 UI에 즉시 반영합니다.
    - 그 후, 비동기 함수 `updateName(newName)`를 호출하여 서버에서 이름을 실제로 업데이트합니다.
    - 업데이트가 완료되면 `onUpdateName(updatedName)`로 최종적으로 변경된 이름을 부모 컴포넌트에 전달합니다.

> 작동방식
- `useOptimistic` 훅은 비동기 작업이 진행되는 동안 즉시 **최적화된 상태(optimistic state)** 를 표시하고, **요청이 완료되거나 오류가 발생하면** React가 자동으로 **현재 상태**로 돌아가게 만듭니다.
    - 예를 들어, 이름이 변경된다고 가정하면, 사용자는 새로운 이름을 바로 보지만, 비동기 요청이 완료되면 실제로 변경된 이름으로 UI가 업데이트됩니다.

> 최종상태를 미리 보여주는 것과 작업이 완료되었을 때의 차이
1. **비동기 작업 중 "최종 상태를 미리 보여주는 것 (Optimistic UI)**
	- 비동기 작업이 진행 중일 때, 사용자는 결과가 끝날 때까지 기다려야 하는데, 이를 기다리면서 UI를 기다리는 동안 아무런 반응도 없는 상태는 사용자 경험을 나쁘게 만들 수 있습니다.
	- 따라서 "비관적 렌더링(Optimistic Rendering)"은 사용자에게 **즉시 최종 상태를 보여주는 방식**입니다.
		- 예를 들어, 이름을 바꾸는 작업을 하고 있을 때, 서버에서 이름을 변경하는 요청을 보내는 동안, 사용자는 새로운 이름을 **미리 볼 수 있습니다**. 
		- 이때 화면에서는 이미 새로운 이름이 반영된 것처럼 보이지만, 실제로 서버에서 이름을 업데이트하는 작업은 아직 진행 중입니다.
		  
	- 이 방식은 사용자가 결과를 **즉시 보는 것**을 의미합니다. 사용자는 "바뀐 이름"을 바로 볼 수 있지만, **서버에서 해당 작업이 완료되기 전까지는 "예상된 결과"일 뿐입니다.**

2. **작업이 완료되었을 때의 UI (실제 상태)**
	- 작업이 끝나면, 서버에서 요청한 대로 이름이 변경됩니다. 그리고 React는 **비동기 작업이 완료된 후 실제 상태를 UI에 반영**합니다. 이때 **미리 보여줬던 최종 상태**와 실제 상태가 **동일한 경우**에는 UI에 변동이 없습니다.
	
	- 하지만, 만약 서버에서 **에러가 발생하면** 미리 보여준 예상된 상태가 **변경되지 않거나 롤백**되기도 합니다. 
		- 예를 들어, 이름 변경 중 에러가 발생하면, 사용자는 실제 상태(변경되지 않은 이름) 를 다시 보게 됩니다.

 3. **요약**
	- "최종 상태를 미리 보여준다는 것"은 비동기 작업을 처리하는 동안 **사용자에게 대기 시간을 줄여주는 방식**을 표현하는 과정일 뿐이며, 실제 서버 응답을 기다리기 전에 "예상되는 결과"를 보여줍니다.
	- **작업이 완료되면** 실제 결과가 화면에 반영되는데, 이때 "미리 보여준 상태"와 **결과가 일치**하거나, 때에 따라 오류가 발생하는 경우 **일치하지 않을 수도 있습니다**(에러 발생 시 롤백).
	- 결론적으로, **비동기 작업이 진행되는 동안 사용자 경험을 개선하기 위해 "미리 보여주는 것"**이 중요한 개념이며, 작업이 완료된 후에는 최종 상태로 자연스럽게 전환됩니다.

---
### 6. use API

- React 19에서는 `use`라는 새로운 API를 도입하여, 리소스를 렌더링 단계에서 읽을 수 있도록 했습니다. 
	- 이 API는 비동기 리소스를 다룰 때 유용하며, 예를 들어 **Promise** 객체를 읽을 때 사용할 수 있습니다. 
	- `use`는 Promise가 해결될 때까지 **컴포넌트를 일시 중단(suspend)** 시키고, 그 후에 값을 반환합니다.
```tsx
import { use } from 'react';

function Comments({ commentsPromise }) {
  // `use`는 commentsPromise가 해결될 때까지 기다립니다.
  const comments = use(commentsPromise);
  return comments.map(comment => <p key={comment.id}>{comment}</p>);
}

function Page({ commentsPromise }) {
  // `Comments`가 suspend되면, 이 Suspense 경계가 표시됩니다.
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  );
}
```
- 위 코드는 `use`를 사용하여 비동기 작업인 **`commentsPromise`** 를 처리한 코드입니다.
	- `use`는 `commentsPromise`의 실행이 완료될 때까지 **일시 중단**하고, 완료 시에 데이터를 반환하여 컴포넌트를 렌더링합니다.

```jsx
import { use } from 'react';
import ThemeContext from './ThemeContext';

function Heading({ children }) {
  if (children == null) {
    return null;
  }

  // 조기 반환 후에도 use를 사용하여 Context 값을 읽을 수 있습니다.
  const theme = use(ThemeContext);
  return (
    <h1 style={{ color: theme.color }}>
      {children}
    </h1>
  );
}
```
- `use`는 **Context**를 읽는 데에도 사용할 수 있는데, 이 API는 조건부로 값을 읽는 데 유용합니다. 
	- 위 코드는, **조기 반환** 후에 컨텍스트 값을 읽는 방식입니다.

- `use`는 조건부로 호출할 수 있어서, **조기 반환** 후에도 값을 읽을 수 있습니다. **`useContext`** 는 조기 반환 후 값을 읽을 수 없지만, `use`는 이런 제약을 피할 수 있습니다.


> 주의사항
- 렌더 함수 외부에서 `use`를 사용하면 경고가 발생합니다.
- 예를 들어, 클라이언트 컴포넌트나 훅에서 **`use`** 를 사용할 수 없으며, **`Suspense`** 호환 라이브러리나 프레임워크에서 Promise를 전달해야만 합니다.
```plaintext
Console
A component was suspended by an uncached promise. Creating promises inside a Client Component or hook is not yet supported, except via a Suspense-compatible library or framework.
```

- 요약하면, `use` API는 **비동기 작업을 처리하고, 렌더링 중에 데이터를 쉽게 소비할 수 있도록** 돕는 새로운 기능입니다. 
	- 이 API를 사용하면 비동기 작업이 완료될 때까지 컴포넌트를 일시 중단시킬 수 있으며, **Suspense와 함께 사용**하여 로딩 상태를 관리할 수 있습니다.

---

### 7. New React DOM Static APIs

- React 19에서는 **정적 사이트 생성(Static Site Generation)** 을 개선하기 위해 두 가지 새로운 API를 `react-dom/static`에 추가했습니다:
	1. **`prerender`**
	2. **`prerenderToNodeStream`**

- 이 새로운 API들은 `renderToString`을 개선한 것으로, **데이터 로딩**을 기다린 후 정적 HTML을 생성하는 기능을 제공합니다. 
	- 특히, **Node.js Streams**와 **Web Streams**와 같은 **스트리밍 환경**에서 사용할 수 있도록 설계되었습니다. 
	- 예를 들어, Web Stream 환경에서 React 트리를 정적 HTML로 미리 렌더링하려면 prerender`를 사용할 수 있습니다.

```js
import { prerender } from 'react-dom/static';

async function handler(request) {
  const { prelude } = await prerender(<App />, {
    bootstrapScripts: ['/main.js']
  });
  return new Response(prelude, {
    headers: { 'content-type': 'text/html' },
  });
}
```
- 이 예시에서 **`prerender`** 함수는 `<App />` 컴포넌트를 미리 렌더링하고, `prelude`를 반환합니다. 
	- `prelude`는 정적 HTML 스트림을 구성하는 부분으로, 이를 응답으로 반환하면 브라우저가 정적 HTML을 로드합니다.
- `bootstrapScripts`는 클라이언트 측 JavaScript 파일을 지정하여, 정적 HTML이 로드된 후 클라이언트 측 React 애플리케이션이 시작될 수 있도록 합니다.

> 중요한 점
- **`prerender`** API는 모든 데이터가 로드될 때까지 기다린 후 정적 HTML 스트림을 반환합니다. 즉, React 컴포넌트가 렌더링되기 전에 필요한 모든 데이터가 준비되어야 하며, 그 후에 HTML이 생성됩니다.
- 반면, 기존의 React DOM 서버 렌더링 API는 **스트리밍 콘텐츠를 실시간으로 로드하면서 렌더링**할 수 있습니다. 새로운 API는 이러한 스트리밍 콘텐츠를 처리하지 않으며, **모든 데이터가 준비된 후에** HTML을 생성합니다.

> 스트리밍(Streaming)과 차이점
- `prerender`와 `prerenderToNodeStream`는 **정적 HTML 생성**을 목표로 하며, 스트리밍 콘텐츠를 처리하지 않습니다.
- 기존의 React DOM 서버 렌더링 API들은 데이터를 로드하면서 **실시간으로 스트리밍 콘텐츠를 전송**할 수 있기 때문에, 더 동적인 렌더링을 지원합니다.

---
### 8. React Server Components

- **React Server Components**는 클라이언트 애플리케이션이나 SSR(서버 사이드 렌더링) 서버와는 별도의 환경에서 **미리 컴포넌트를 렌더링**할 수 있는 새로운 기능입니다. 
	- 이때 "서버"는 **React Server Components**가 실행되는 환경을 의미합니다. 즉, React Server Components는 빌드 시 한 번 실행하거나, 각 요청마다 웹 서버에서 실행할 수 있습니다.

> 주요 내용
1. **React Server Components**의 도입:
    - **React Server Components**는 클라이언트 애플리케이션과 별개의 서버 환경에서 컴포넌트를 미리 렌더링할 수 있게 해줍니다. 
    - 이는 **빌드 타임**에 CI 서버(지속적 통합(Continuous Integration) 프로세스를 자동화하고 관리하는 서버)에서 한 번 실행되거나, **웹 서버**에서 요청이 있을 때마다 실행될 수 있습니다.

2. **React 19에서의 지원**:
    - **React 19**에서는 **Canary 채널**에서 제공되던 React Server Components의 모든 기능이 포함되었습니다. 이로 인해, **React Server Components**를 사용하는 라이브러리들이 이제 React 19을 대상으로 한 **동적 의존성**을 가질 수 있게 되었습니다. `react-server` export condition을 통해 **Full-stack React Architecture**를 지원하는 프레임워크에서 사용할 수 있습니다.

3. **Server Components의 안정성**:
    - React 19에서 **React Server Components**는 안정적인 기능으로 제공되며, **주요 버전** 사이에서는 호환성 문제가 발생하지 않습니다. 그러나 **React Server Components를 위한 번들러**나 프레임워크에서 사용되는 기본 API들은 **Semver를 따르지 않**아, **마이너 버전**에서 문제가 발생할 수 있습니다.
        
    - **번들러나 프레임워크**에서 React Server Components를 지원하려면 **특정 React 버전**을 고정시키거나 **Canary 릴리스를 사용**하는 것이 권장됩니다. 앞으로 번들러와 프레임워크들이 React Server Components를 안정적으로 구현할 수 있도록 계속 개선될 예정입니다.


> 요약
- React Server Components는 React 애플리케이션의 **서버 측 렌더링**을 새로운 방식으로 지원합니다. 
- 이는 클라이언트와 별개로 서버에서 미리 렌더링된 컴포넌트를 제공할 수 있게 해주며, React 19에서는 이 기능이 안정적으로 포함되었습니다. 
- 하지만 React Server Components를 위한 **API**는 아직 완전히 안정화되지 않았기 때문에, 해당 기능을 사용하는 **번들러나 프레임워크**는 특정 React 버전과 호환성 문제를 피하기 위해 버전을 고정하는 것이 좋습니다.

---
### 9. Server Actions (여기부터 추가)

- 클라이언트 컴포넌트에서 서버에서 실행되는 비동기 함수를 호출할 수 있는 Server Actions 기능이 추가되었습니다. `"use server"` 지시어를 사용하여 서버에서 실행되는 함수의 참조를 클라이언트에 전달할 수 있습니다.

---
### 10. Ref as a Prop

- 함수형 컴포넌트에서 `ref`를 속성으로 전달할 수 있게 되었으며, 더 이상 `forwardRef`가 필요하지 않습니다. 이 변경은 리팩토링을 통해 기존 코드에 자동으로 적용할 수 있습니다.
```tsx
function MyInput({placeholder, ref}) {  
	return <input placeholder={placeholder} ref={ref} />  
}  

//...  

<MyInput ref={ref} />
```


### 11. Hydration Error 개선

- React 19에서는 Hydration 오류를 더 잘 처리하고, 오류 발생 시 더욱 유용한 정보를 제공합니다.


### 12. `<Context>` as a Provider

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


### 13. Ref 콜백에 정리 함수 추가

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


### 14. useDeferredValue 초기 값 옵션

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


### 15. 문서 메타데이터 지원

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


### 16. 스타일시트 지원

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


### 17. 비동기 스크립트 지원

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


### 18. 자원 사전 로딩 지원

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


### 19. 타사 스크립트 및 확장 프로그램과의 호환성

- 타사 스크립트 및 브라우저 확장 프로그램이 추가한 예기치 않은 요소들로 인한 Hydration 오류가 발생하지 않도록 개선되었습니다.


### 20. 향상된 오류 보고

- React 19에서는 오류 보고가 개선되어 중복된 오류를 제거하고, 모든 관련 정보를 포함한 단일 오류 메시지를 제공합니다.


### 21. 사용자 정의 요소 지원

- React 19는 사용자 정의 요소(Custom Elements)에 대한 완전한 지원을 추가했습니다. 서버 사이드 렌더링 시, 기본 데이터 유형은 속성으로 렌더링되고, 비기본 유형은 생략됩니다.