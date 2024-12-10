https://react.dev/blog/2024/12/05/react-19
참고 : https://www.intelligencelabs.tech/062b17fe-7b82-4bae-ba7f-0c17baca1bab#8e7b70dc-2a82-42e6-8048-a2062cf5426f

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
- 이전에 Canary 버전에서 사용되던 `ReactDOM.useFormState`라는 이름으로 사용되었습니다. 

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

> 이전
```html
<form action="/examples/media/action_target.php">
	이름 : <input type="text" name="st_name"><br>
	학번 : <input type="text" name="st_id"><br>
	학과 : <input type="text" name="department"><br>
	<input type="submit">
</form>
```

> 이후
```jsx
<form action={actionFunction}>
{/* ... */}
</form>
```

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
1. `useFormStatus` 훅은 부모 `<form>`을 **Context provider**처럼 취급하여, 해당 폼의 상태를 읽을 수 있게 해줍니다.
2. 즉, 폼의 상태를 관리하는 컴포넌트에서 `useFormStatus`를 호출하면, 그 부모 폼의 상태를 자동으로 가져오게 되는 방식입니다.

---
### 5. `useOptimistic` Hook

- 데이터를 변경할 때, 비동기 작업이 진행되는 동안 **최종 상태를 미리 보여주는 UI 패턴**은 사용자 경험을 향상시키는 데 유용합니다. React 19에서는 이 패턴을 더 쉽게 구현할 수 있도록 **`useOptimistic`** 훅을 추가했습니다.
```tsx
function ChangeName({ currentName, onUpdateName }) {
  const [optimisticName, setOptimisticName] = useOptimistic(currentName);

  const submitAction = async (formData) => {
    const newName = formData.get("name");
    setOptimisticName(newName); // 낙관적 상태 업데이트

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
1. 렌더 함수 외부에서 `use`를 사용하면 경고가 발생합니다.
2. 예를 들어, 클라이언트 컴포넌트나 훅에서 **`use`** 를 사용할 수 없으며, **`Suspense`** 호환 라이브러리나 프레임워크에서 Promise를 전달해야만 합니다.
```plaintext
Console
A component was suspended by an uncached promise. Creating promises inside a Client Component or hook is not yet supported, except via a Suspense-compatible library or framework.
```

3. 요약하면, `use` API는 **비동기 작업을 처리하고, 렌더링 중에 데이터를 쉽게 소비할 수 있도록** 돕는 새로운 기능입니다. 
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
1. **`prerender`** API는 모든 데이터가 로드될 때까지 기다린 후 정적 HTML 스트림을 반환합니다. 즉, React 컴포넌트가 렌더링되기 전에 필요한 모든 데이터가 준비되어야 하며, 그 후에 HTML이 생성됩니다.
2. 반면, 기존의 React DOM 서버 렌더링 API는 **스트리밍 콘텐츠를 실시간으로 로드하면서 렌더링**할 수 있습니다. 새로운 API는 이러한 스트리밍 콘텐츠를 처리하지 않으며, **모든 데이터가 준비된 후에** HTML을 생성합니다.

> 스트리밍(Streaming)과 차이점
1. `prerender`와 `prerenderToNodeStream`는 **정적 HTML 생성**을 목표로 하며, 스트리밍 콘텐츠를 처리하지 않습니다.
2. 기존의 React DOM 서버 렌더링 API들은 데이터를 로드하면서 **실시간으로 스트리밍 콘텐츠를 전송**할 수 있기 때문에, 더 동적인 렌더링을 지원합니다.

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
1. React Server Components는 React 애플리케이션의 **서버 측 렌더링**을 새로운 방식으로 지원합니다. 
2. 이는 클라이언트와 별개로 서버에서 미리 렌더링된 컴포넌트를 제공할 수 있게 해주며, React 19에서는 이 기능이 안정적으로 포함되었습니다. 
3. 하지만 React Server Components를 위한 **API**는 아직 완전히 안정화되지 않았기 때문에, 해당 기능을 사용하는 **번들러나 프레임워크**는 특정 React 버전과 호환성 문제를 피하기 위해 버전을 고정하는 것이 좋습니다.

---
### 9. Server Actions (여기부터 추가)

- Server Actions는 **클라이언트 컴포넌트**가 서버에서 실행되는 비동기 함수를 호출할 수 있도록 하는 기능입니다.

> 작동 방식
1. **"use server" 지시문 사용**:
    - `use server`라는 지시문을 사용하여 Server Action을 정의하면, 프레임워크가 자동으로 해당 서버 함수에 대한 참조를 생성합니다.
    - 이 참조는 클라이언트 컴포넌트로 전달됩니다.

2. **클라이언트에서 호출**:
    - 클라이언트에서 이 함수가 호출되면 React는 서버에 요청을 보내 해당 함수를 실행하고, 결과를 반환받아 클라이언트에 전달합니다.


> 중요한 주의사항
1. **Server Components와 혼동하지 말 것**:
    - `use server`는 **Server Components**를 나타내는 것이 아닙니다.
    - 이 지시문은 **Server Actions**에만 사용됩니다.
    - Server Components는 별도의 지시문 없이 작동합니다.

2. Server Actions와 Server Components는 다른 개념이므로 각각의 용도를 구분해야 합니다.


> Server Actions의 사용법
1. **Server Components에서 생성**:
    - Server Actions는 Server Components 내부에서 정의할 수 있습니다.
    - 그런 다음, 이를 Client Components의 **props**로 전달하여 사용할 수 있습니다.

2. **Client Components에서 직접 사용**:
    - Server Actions는 Client Components에 **import**하여 바로 사용할 수도 있습니다.

- 더 자세한 내용은 [React Server Actions](https://react.dev/) 및 지시문(Directives)에 대한 공식 문서를 참고하면 됩니다.

---
### 10. Ref as a Prop

- React 19부터는 ***함수형 컴포넌트에서만*** `ref`를 **prop으로 직접 전달**할 수 있습니다.  
	- 이제 별도로 `forwardRef`를 사용하지 않아도 됩니다. (개꿀)
```tsx
function MyInput({ placeholder, ref }) {
  return <input placeholder={placeholder} ref={ref} />;
}

// 사용 예시
<MyInput placeholder="입력하세요" ref={ref} />;

```
- `ref`가 함수형 컴포넌트의 일반 prop처럼 동작하며, `forwardRef`를 사용했던 기존 방식보다 더 간단하고 직관적으로 변경되었습니다.

```tsx
const MyInput = React.forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```
- 이전까지는(React 19 이전) `forwardRef`를 사용해야만 `ref`를 자식 컴포넌트로 전달할 수 있었습니다.
	- React 19부터는 이 `forwardRef`를 사용할 필요가 없으며, **미래의 React 버전**에서 `forwardRef`는 **사용 중단(Deprecate)** 및 **제거(Remove)** 될 예정이라고 합니다.

---
### 11. Hydration Error 개선

- React 19에서는 Hydration 오류를 더 잘 처리하고, 오류 발생 시 더욱 유용한 정보를 제공합니다.
	- Hydration은 서버에서 렌더링된 HTML과 클라이언트에서 렌더링된 React 트리가 일치하도록 만드는 과정입니다.  만약, 오류가 발생하면 클라이언트는 서버의 HTML을 대체하거나, 트리를 재생성합니다.

![[react-19 hydration 에러 (이전).png]]
- 이전 hydration 오류 메시지의 **문제점**은 아래와 같았습니다.
	- 같은 내용의 메시지가 여러 번 출력되어 디버깅이 어려움
	- 불일치의 원인에 대한 정보가 부족

![[react-19 hydration 에러 (이후).png]]
- React19부터는 아래와 같은 주요 개선점이 추가되었습니다.
	- **단일 메시지**
	    - 여러 메시지를 하나로 통합해 **중복 로그 제거**
	
	- **불일치 원인에 대한 세부 정보 제공**
	    - Hydration 실패의 가능한 원인을 나열
	        - 클라이언트/서버 간 분기 로직(`if (typeof window !== 'undefined')`)
	        - 매번 달라지는 값 (`Date.now()`, `Math.random()`)
	        - 사용자 로케일에 따른 날짜 형식 불일치
	        - 외부 데이터를 동기화하지 않은 경우
	        - 잘못된 HTML 태그 중첩
	        - 브라우저 확장 프로그램의 간섭
	
	- **HTML Diff 표시**
		- 서버와 클라이언트 HTML 간 차이를 시각적으로 보여줌
```arduino
  <App>
    <span>
  +    Client
  -    Server
```

---
### 12. `<Context>` as a Provider

##### 1. 기존방식
- 기존에는 React에서 Context를 사용하려면 `<Context.Provider>`를 작성해야 했습니다.
```jsx
const ThemeContext = createContext('');

function App({ children }) {
  return (
    <ThemeContext.Provider value="dark">
      {children}
    </ThemeContext.Provider>
  );
}
```

##### 2. 변경된 방식
- React 19부터는 `<Context>` 자체를 제공자로 사용할 수 있습니다.  
	- 이제 `<Context.Provider>`를 작성하지 않아도 됩니다. (코드가 간결해지고 직관적이게 되었음)
```jsx
const ThemeContext = createContext('');

function App({ children }) {
  return (
    <ThemeContext value="dark">
      {children}
    </ThemeContext>
  );
}
```

---
### 13. Ref 콜백에 정리 함수(클린업 함수) 추가

- React 19에서는 **ref 콜백 함수**에서 **정리(cleanup) 함수**를 반환할 수 있습니다.  
	- 이 정리 함수는 컴포넌트가 **DOM에서 제거(unmount)** 될 때 호출됩니다.
	- React19 이전방식은, 컴포넌트가 제거될 때 React가 ref 콜백에 null을 전달했지만, React19부터는 클린업 함수가 있으면 더이상 React는 ref 콜백에 null을 전달하지 않습니다.
```tsx
<input
  ref={(ref) => {
    // ref가 생성됨
    console.log('ref created:', ref);
    
    // NEW: 컴포넌트가 제거될 때 호출될 cleanup 함수 반환
    return () => {
      console.log('ref cleaned up:', ref);
    };
  }}
/>

```

> 주의사항
- TypeScript는 반환값이 cleanup 함수인지, 단순히 반환한 값인지 구분할 수 없습니다. 때문에, ref 콜백에서 cleanup 함수 이외의 반환값은 이제 TypeScript에서 **거부**됩니다.
- 즉, 암묵적인 반환(implicit return)을 사용하면 TypeScript 오류가 발생합니다.
```tsx
<div ref={(current) => (instance = current)} /> // TypeScript 에러
<div ref={(current) => { instance = current }} /> // 명시적 반환 사용
```

---
### 14. useDeferredValue 초기 값 옵션

- `useDeferredValue`는 UI의 일부 업데이트를 지연시킬 수 있도록 해주는 React Hook입니다
	- 이 Hook을 사용하면 일부 콘텐츠가 최신 상태로 로드되는 동안 이전 콘텐츠를 표시하고, UI의 일부를 재렌더링하는 것을 지연시킬 수 있습니다.
	- 주요 내용은 [여기](https://react.dev/reference/react/useDeferredValue)를 참고해주세요.

- `useDeferredValue`의 기능은 아래와 같습니다.
	- **구식 콘텐츠 표시**: 최신 콘텐츠가 로드되는 동안 구식 콘텐츠를 표시할 수 있습니다.
	- **UI의 일부 업데이트 지연**: UI의 일부를 지연시켜서 렌더링을 최적화할 수 있습니다

- React19부터는 `useDeferredValue` 훅에 초기 값 옵션(`initialValue`)이 추가되었습니다. 이 옵션을 사용하면 초기 렌더링에서 기본 값을 사용할 수 있습니다.

> 이전 방식
```jsx
function Search({ deferredValue }) {
  const value = useDeferredValue(deferredValue);
  return <Results query={value} />;
}
```
- 해당 코드에서는, 초기 렌더링에서 `deferredValue`가 바로 사용됩니다.
	- 초기 상태가 없으면 React는 `deferredValue`를 바로 사용하기 때문에, 떄떄로 불필요한 초기상태로 렌더링을 시킬 수 있었습니다.

> 변경된 방식
```tsx
function Search({ deferredValue }) {
  // 초기 렌더링 시 값은 ''.
  // 이후 deferredValue로 다시 렌더링이 예약됩니다.
  const value = useDeferredValue(deferredValue, '');
  
  return (
    <Results query={value} />
  );
}
```
-  이제, 컴포넌트는 초기 렌더링 시 빈 문자열(`''`)을 사용하고, 백그라운드에서 지연된 값을 업데이트합니다.


### 15. 문서 메타데이터 지원

- HTML에서 `<title>`, `<link>`, `<meta>`와 같은 문서 메타데이터 태그는 `<head>` 섹션에 배치되는 것으로 예약되어 있습니다. 
	- React에서는 메타데이터가 앱에 적합한지 여부를 결정하는 컴포넌트가 `<head>`를 렌더링하는 위치와 매우 멀리 있을 수 있습니다. 
	- 또는 React가 아예 `<head>`를 렌더링하지 않기도 합니다. 이전에는 이러한 요소들을 `useEffect`나 `react-helmet` 같은 라이브러리를 사용하여 수동으로 삽입해야 했으며, 서버에서 React 애플리케이션을 렌더링할 때 이를 신중하게 처리해야 했습니다.

- **React 19**에서는 컴포넌트 내에서 문서 메타데이터 태그를 기본적으로 렌더링하는 기능을 추가했습니다.
```tsx
function BlogPost({ post }) {
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
- 이 컴포넌트를 React가 렌더링할 때, `<title>`, `<link>`, `<meta>` 태그를 인식하고 자동으로 이를 문서의 `<head>` 섹션으로 이동시킵니다. 이러한 메타데이터 태그를 기본적으로 지원함으로써, React는 클라이언트 전용 앱, 스트리밍 SSR(서버 사이드 렌더링), 서버 컴포넌트와 함께 제대로 동작하도록 보장할 수 있습니다.

---
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
      <link rel="stylesheet" href="bar" precedence="high" />
      <article className="foo-class bar-class">
        {...}
      </article>
    </Suspense>
  );
}

function ComponentTwo() {
  return (
    <div>
      <p>{...}</p>
      <link rel="stylesheet" href="baz" precedence="default" />  {/* foo와 bar 사이에 삽입됨 */}
    </div>
  );
}

```
- `ComponentOne`에서는 두 개의 스타일시트를 로드하며, 각 스타일시트는 우선순위(`precedence`)가 지정됩니다.
- `ComponentTwo`에서 로드되는 스타일시트는 `ComponentOne`에서 로드된 `foo`와 `bar` 스타일시트 사이에 삽입됩니다.
	- `ComponentOne` 로드됨 -> `ComponentTwo` 로드됨
	- 여기서, 스타일 시트는 로드 순서에 따라 `"foo" -> "baz" -> "bar"`가 된다.

>SSR (서버 사이드 렌더링)에서의 스타일 시트
- 서버 사이드 렌더링 시, React는 `<head>`에 스타일시트를 포함시켜 브라우저가 해당 스타일시트가 로드될 때까지 화면을 그리지 않도록 합니다. 
- 만약 스타일시트가 스트리밍 도중 늦게 발견되면, React는 클라이언트에서 해당 스타일시트를 `<head>`에 삽입하여 그 스타일을 필요로 하는 Suspense 경계의 콘텐츠가 표시되기 전에 로드되도록 보장합니다.

> CSR(클라이언트 사이드 렌더링)에서의 스타일 시트
- 클라이언트 사이드 렌더링 시, React는 새로 렌더링된 스타일시트가 로드될 때까지 렌더링을 완료하지 않고 기다립니다. 만약 이 컴포넌트가 애플리케이션 내 여러 위치에서 렌더링되더라도, React는 스타일시트를 문서에 한 번만 포함시킵니다.
```jsx
function App() {
  return <>
    <ComponentOne />
    ...
    <ComponentOne /> {/* DOM에서 중복된 스타일시트 링크가 생기지 않음 */}
  </>;
}
```

---
### 17. 비동기 스크립트 지원

- HTML에서 일반적인 스크립트(`<script src="...">`)와 지연된 스크립트(`<script defer="" src="...">`)는 문서의 로드 순서에 따라 로드됩니다. 
	- 이로 인해 컴포넌트 트리 내 깊은 위치에 있는 스크립트들을 렌더링하는 것이 어려울 수 있습니다. 반면, 비동기 스크립트(`<script async="" src="...">`)는 임의의 순서로 로드됩니다.

- **React 19**에서는 비동기 스크립트에 대한 지원을 개선하여, **스크립트가 실제로 의존하는 컴포넌트 내에서 스크립트를 렌더링**할 수 있게 하고, 스크립트 인스턴스를 재배치하거나 중복 제거하는 작업을 직접 관리하지 않을 수 도록 했습니다.
```tsx
function MyComponent() {
  return (
    <div>
      <script async={true} src="..." />
      Hello World
    </div>
  );
}

function App() {
  return (
    <html>
      <body>
        <MyComponent />
        ...
        <MyComponent /> {/* DOM에서 중복된 스크립트가 생성되지 않음 */}
      </body>
    </html>
  );
}
```

> 렌더링 환경에서의 동작
1. **서버 사이드 렌더링 (SSR)**: 비동기 스크립트는 `<head>`에 포함되어 스타일시트, 폰트, 이미지 사전로드(preload) 등과 같은 화면 렌더링을 차단하는 중요한 리소스 뒤에서만 로드 우선순위가 지정됩니다.

2. **여러 컴포넌트에서 렌더링 시 중복 방지**: 비동기 스크립트는 **여러 컴포넌트에서 렌더링되더라도 중복을 방지하여 React가 해당 스크립트를 한 번만 로드하고 실행하도록 보장**합니다.

---
### 18. 자원 사전 로딩 지원

- 문서 초기 로드 및 클라이언트 측 업데이트 시, 브라우저에 필요할 리소스를 가능한 한 일찍 로드하도록 알려주는 것은 페이지 성능에 큰 영향을 미칠 수 있습니다. 
	- React 19에서는 브라우저 리소스를 로드하고 사전 로딩할 수 있는 새로운 API들이 포함되었으며, 이 API들은 비효율적인 리소스 로딩에 의해 성능이 저하되지 않도록 돕습니다.
```js
import { prefetchDNS, preconnect, preload, preinit } from 'react-dom';

function MyComponent() {
  preinit('https://.../path/to/some/script.js', { as: 'script' }); // 이 스크립트를 미리 로드하고 실행합니다
  preload('https://.../path/to/font.woff', { as: 'font' }); // 이 폰트를 미리 로드합니다
  preload('https://.../path/to/stylesheet.css', { as: 'style' }); // 이 스타일시트를 미리 로드합니다
  prefetchDNS('https://...'); // 이 호스트에서 실제로 요청하지 않지만 DNS를 미리 로드합니다
  preconnect('https://...'); // 이 호스트에서 뭘 요청할지는 모르지만 미리 연결을 준비합니다
}

```

> 위 코드가 결과적으로 생성하는 DOM/HTML 예시
```html
<html>
  <head>
    <!-- 링크/스크립트는 호출 순서가 아니라, 초기 로딩에 중요한 리소스를 기준으로 우선순위가 설정됩니다 -->
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

> API 사용으로 인한 장점
1. **초기 페이지 로드 최적화**: 스타일시트 로딩과 별도로 추가 리소스(예: 폰트)의 발견을 미루지 않고 미리 로드함으로써 초기 페이지 로딩을 최적화할 수 있습니다.
2. **클라이언트 업데이트 속도 향상**: 예측되는 네비게이션에 사용될 리소스 목록을 미리 패칭(prefetch)하고, 클릭하거나 심지어 호버할 때 이를 미리 로드하여 업데이트 속도를 빠르게 만들 수 있습니다.

---
### 19. 타사 스크립트 및 확장 프로그램과의 호환성

- React 19에서는 **Hydration Process**를 개선하여 타사 스크립트와 브라우저 확장 프로그램이 렌더링에 미치는 영향을 잘 처리할 수 있도록 했습니다.

> Hydration 과정에서의 개선점
- Hydration이 이루어질 때, 클라이언트에서 렌더링된 요소가 서버에서 전달된 HTML의 요소와 일치하지 않으면, React 19에서는 **클라이언트에서 재렌더링을 강제하여 콘텐츠를 수정합니다**. 
- 이전에는 타사 스크립트나 브라우저 확장 프로그램이 삽입한 요소가 있을 경우, 일치하지 않는 오류가 발생하고, 이로 인해 클라이언트에서 렌더링을 다시 수행해야 했습니다.

> React 19의 개선사항
1. **예상치 못한 `<head>` 및 `<body>` 태그 처리**: React는 이제 Hydration 중에 예상치 못한 태그를 건너뛰어, 일치하지 않는 오류를 방지합니다.

2. **타사 스크립트와 확장 프로그램의 스타일 시트 보존**: 만약 React가 Hydration 중에 전체 문서를 재렌더링해야 하는 경우, 타사 스크립트나 브라우저 확장 프로그램에 의해 삽입된 스타일 시트는 그대로 두어, 페이지가 제대로 렌더링됩니다.

---
### 20. 향상된 오류 보고

- React 19에서는 오류 처리 방식을 개선하여 **중복을 제거**하고, 처리된 오류와 처리되지 않은 오류에 대해 다양한 옵션을 제공하도록 했습니다.

---
### 21. 커스텀 엘리먼트(Custom Elements) 지원

- React 19에서는 **커스텀 엘리먼트(Custom Elements)** 에 대한 완전한 지원을 추가하였으며, **Custom Elements Everywhere**의 모든 테스트를 통과하였습니다.
	- 커스텀 엘리먼트란 **웹 컴포넌트(Web Components)** 의 한 종류로, 사용자가 정의한 HTML 태그나 컴포넌트를 말합니다. 
	- 즉, React를 사용할 때 기존의 HTML 태그(`<div>`, `<span>` 등) 대신에 개발자가 만든 새로운 HTML 태그(`<my-button>`, `<user-card>` 등)를 사용할 수 있도록 해주는 기술입니다.

- 이전 버전에서는 React에서 커스텀 엘리먼트를 사용할 때 문제가 있었습니다. React는 인식하지 못하는 속성(props)을 **속성(attribute)** 으로 처리했기 때문에, **속성(properties)** 으로 처리해야 하는 경우에 어려움이 있었습니다.

> 개선점
- React 19에서는 서버 사이드 렌더링(SSR)과 클라이언트 사이드 렌더링(CSR) 모두에서 작동하는 **속성(properties)** 지원을 추가했습니다. 이에 대한 전략은 다음과 같습니다.

- **서버 사이드 렌더링(SSR)**:
    - 원시 값(primitive value)인 문자열, 숫자 또는 `true` 값을 가진 props는 **속성(attribute)** 으로 렌더링됩니다.
    - 객체, 심볼(symbol), 함수, 또는 `false`와 같은 비원시 타입(non-primitive type)을 가진 props는 **생략**됩니다.

- **클라이언트 사이드 렌더링(CSR)**:
    - 커스텀 엘리먼트 인스턴스에 해당하는 속성(properties)을 가진 props는 **속성(properties)** 으로 할당됩니다.
    - 해당 속성이 없다면, **속성(attribute)** 으로 할당됩니다.

- 자세한 내용은 [여기](https://react.dev/blog/2024/04/25/react-19-upgrade-guide)를 참고해주세요.