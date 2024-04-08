
#### 1. Overview

 - [Next.js14](https://nextjs.org/blog/next-14#nextjs-learn-course)버전에 대한 업데이트 내용을 보던 중 **server-action(stable)** 이라는 내용이 문득 눈에 띄었다. 
	 - 안정화되지 않았을 때에는 "오, 이런 게 나왔구나"라고만 하고 넘어갔기 때문에, 포스트를 작성하는 이 시점까지도 이게 뭔지 참 궁금했다.
 
 - 이 참에 사용해보면서 해당 기능에 대해 알아보고, 거기다 기록까지 해 두면 좋을 것 같아 포스트를 작성하게 되었다.

- 참고링크는 다음과 같다.
	- [공식 문서](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations)


#### 2. Server Actions?

- Next.js의 공식문서에서는 ***Server Actions***에 대해 아래와 같이 소개하고 있다.

> - ***Server Actions***은 서버에서 실행되는 비동기 함수입니다. 
> - 이는 Next.js 애플리케이션에서 Form 제출과 데이터 변경을 다루는 데 사용될 수 있습니다.

- 필자는 개념을 보는 이 타이밍에 궁금한 것이 생겼다.
	- 서버에서 실행되는 비동기 함수가 어떻게 Form 제출 및 데이터 변경과 연관될 수 있는가?
	- 지금까지 사용자 입력 이벤트로 인해 저장되어야 하는 데이터는 ***react hooks in RCC***들로 관리를 해왔었기에, 적어도 나는 이 개념에 대해 **굉.장.히** 궁금했다.

- Next.js v13.4 이상에서 작동하며, stable 버전인 Next.js v14.0.0 이상이 아니라면 아래의 코드를 `next.config.js`에 추가해주어야 한다.
```js
module.exports = { experimental: { serverActions: true, }, };
```


#### 3. 규칙

- ***Server Action***은 React의 `"use server"` 지시문을 사용하여 정의될 수 있다. 
	- 이 지시문을 비동기 함수의 맨 위에 배치하면, 해당 함수가 **Server Action으로 지정된 함수다!** 라고 표시할 수 있다.
	- 혹은, 별도의 파일의 맨 위에 배치하여 해당 파일의 모든 내보내기를 Server Action으로 표시할 수 있다.

- Server Action을 사용하기 위해서는 준수해야 하는 규칙이 조금 있다. 먼저 서버 컴포넌트부터 살펴보자.

#### 3-1. Server Components

- 서버 컴포넌트는 **인라인 함수 레벨(단위) 또는 모듈 레벨(단위)** 의 `"use server"` 지시문을 사용할 수 있다.
	- Server Action을 인라인으로 추가하려면, 아래 코드와 같이 함수 본문의 맨 위에 `"use server"`를 추가하면 된다.

```tsx
import ClientComponent from "./ClientComponent";

// Server Component
export default function Page() {
  // Server Action
  async function create() {
    "use server";

    // ...
  }

  return <>{/* ... */}</>;
}
```

#### 3-2. Client Componets

- 클라이언트 컴포넌트는 **모듈 레벨**의 `"use server"` 지시문을 사용하는 액션만 가져올 수 있다. 
- ***완전 중요!! 클라이언트 컴포넌트는 모듈 레벨의 use server 지시문으로 사용된 액션만 가져올 수 있다!***
```ts
"use server";

export async function create() {
  const res = fetch("https://jsonplaceholder.typicode.com/todos/1").then(
    (response) => response.json()
  );
  
  return res;
}
```

- 클라이언트 컴포넌트에서 Server Action을 호출하려면, 새 파일을 만들고 맨 위에 `"use server"` 지시문을 추가하여야 한다.
	- **파일 내의 모든 함수**는 클라이언트 및 서버 컴포넌트에서 재사용될 수 있는 Server Action으로 표시된다.
```tsx
// import
import { create } from "@/app/utils/action/actions";

export function Button() {
  return <div>{/* ... */}</div>;
}
```

- Server Action은 클라이언트 컴포넌트에 `props`로도 전달할 수 있다.
	- 이 내용이 은근히 중요하다. 서버 컴포넌트에서 사용할 수 있는 Data Fetch와 같은 함수는 클라이언트 컴포넌트로 `props`를 전달할 수 없다.
	- 왜냐하면, 서버 컴포넌트는 서버에서 해석되는 과정에서 **직렬화**의 과정을 거치기 때문이다. 그리고 함수는 직렬화할 수 없는 대표적인 요소이다.
	- 그런데, 특정함수 요소가 **모듈 레벨**로 `"use server"`지시문과 함께 Server Action으로 지정되면 클라이언트 컴포넌트의 `props`로 전달 가능하다는 것이다. (미쳤다)


#### 4. Behavior (동작)

- Server Action을 사용한 요소의 동작은 아래의 특징을 가진다..

1. Server Action은 `<form>` 요소의 **action** 속성을 사용하여 호출할 수 있다.

2. 서버 컴포넌트는 기본적으로 점진적인 개선을 지원한다. 즉, JavaScript가 아직로드되지 않았거나 비활성화되어 있더라도 Form이 제출된다는 것이다.

3. 클라이언트 컴포넌트에서 Server Action을 호출하는 Form은 JavaScript가 아직 로드되지 않은 경우 제출을 대기시킨다, 
	- 이는 클라이언트 컴포넌트에서의 Hydration 과정을 우선시한다는 뜻이다. 
	- Hydration 후에는 브라우저가 Form 제출 시 리프레시하는 과정이 없다. 

4. Server Action은 `<form>`에만 제한되지 않으며, **이벤트 핸들러, useEffect, 서드파티 라이브러리** 및 `<button>`과 같은 다른 **Form 요소**에서 호출될 수 있다.

5. Server Action은 Next.js의 Caching(캐싱) 및 revalidation 아키텍처와 통합되어 사용된다.
	- 액션이 호출될 때 Next.js는 단일 서버 라운드트립에서 업데이트된 UI와 새 데이터를 동시에 반환할 수 있다.

6. 내부적으로 액션은 **POST** method를 사용하며, 이 HTTP method만이 액션을 호출할 수 있다. 

7. Server Action의 인수(argument) 및 반환 값(return value)은 **React에 의해 직렬화될 수 있어야 한다.**
	- 직렬화는 [[SSR, CSR, RSC, RCC]] 내용을 참고해보자.

8. Server Action은 **함수**이기 때문에, 애플리케이션의 어디에서나 재사용될 수 있다.

9. Server Action은 사용되는 페이지나 레이아웃으로부터 **런타임을 상속받는다.**
	- 이는, Server Action은 실행될 때 **해당 페이지나 레이아웃이 사용하는 실행 환경을 그대로 이어받는다는 의미**이다.
	- Server Action은 페이지나 레이아웃에서 사용되는 컨텍스트와 설정 등을 **공유**할 수 있게 해준다.

10. Server Action은 사용되는 페이지나 레이아웃으로부터 Route Segment Config를 상속받는다. 이는 maxDuration과 같은 필드를 포함한다.
	- **Route segment**에 대한 내용은 [Route Segment]를 참고하자. 
	- Route Segment Config에는 페이지나 레이아웃의 **Route Segment**에 대한 설정이 포함된다.
	- Server Action은 페이지나 레이아웃의  **Route Segment**설정을 활용하여, 적절한 동작을 수행하거나 이러한 설정을 고려할 수 있다.


#### 5. Forms 사용과 예시
 
- React는 HTML `<form>` 요소를 확장하여 Server Action을 action 속성으로 호출할 수 있게 한다.
	- Form에서 호출될 때 action은 자동으로 FormData객체를 받는데, 필드를 관리하기 위해 **React의 `useState hook`을 사용할 필요가 없으며, 대신 네이티브 FormData 메소드를 사용**하여 데이터를 추출할 수 있다.

```tsx
// Server Component
export default function Page() {
  // 함수안에 'use server' 를 작성해두면 그 함수 내용을 자동으로 서버 API로 만들어 준다.
  // 함수안에 있는 코드는 유저에게 노출되는 코드가 아니라 서버코드기 때문에, 거기서 맘대로 폼내용을 DB에 저장하거나 검사해도 되는 장점이 있다.
  async function createInvoice(formData: FormData) {
    "use server";

    const rawFormData = {
      title: formData.get("title"),
      body: formData.get("body"),
      userId: formData.get("userId"),
    };

    console.log("raw : ", rawFormData);

    // mutate data(데이터 업데이트)
    fetch("https://jsonplaceholder.typicode.com/posts", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(rawFormData),
    })
      .then((response) => response.json())
      .then((json) => {
        console.log("jsonData is ", json);
      });

    // revalidate cache
  }

  return (
    // Form 전송 시, action 전달 함수 실행
    <form action={createInvoice}>
      <input type="text" name="title" />
      <input type="text" name="body" />
      <input type="text" name="userId" />
      <button type="submit">POST</button>
    </form>
  );
}

```


#### 6. 추가 인수 전달

- 자바스크립트의 `bind` 메소드를 사용하면, Server Action에 추가 인수를 전달할 수 있다.
```js
const boundFunction = someFunction.bind(thisArg, arg1, arg2, ...);
```

##### 6-1. `.bind()` 메소드

- `bind()` 메소드는 JavaScript의 내장된 메소드 중 하나로, 함수를 호출할 때 특정한 컨텍스트로 설정하고, 해당 함수에 인수를 전달할 때 사용된다.
	1. `someFunction`: 바인딩할 함수이다.
	2. `thisArg`: 함수 내부에서 `this` 키워드가 참조할 객체이다. 이는 바인딩된 함수가 호출될 때 함수 내부에서 `this`로 사용된다.
	3. `arg1`, `arg2`, `...`: 바인딩된 함수에 전달할 추가 인수

- `.bind()` 메소드를 사용하면 원본 함수를 호출하는 대신 새로운 함수를 생성한다.
	- 이 새로운 함수는 지정된 `this` 값으로 호출될 때 원본 함수의 복사본이며, 추가 인수가 바인딩된 함수에 전달된다.
	- 예시는 아래와 같다.

```js
function greet(name) {
  console.log(`Hello, ${name}!`);
}

const greetBob = greet.bind(null, 'Bob');
greetBob(); // 출력: Hello, Bob!
```
- 위의 예제는 `bind()` 메소드를 사용하여 `greet()` 함수에 'Bob'이라는 인수를 고정시켜 새로운 함수 `greetBob`을 생성하고 있다. 
	- 이렇게 생성된 `greetBob` 함수는 항상 'Bob'을 인수로 받아 호출된다.

#### 6-2. Server Action 예제

- 이제, `bind()` 메소드에 대해 알아봤으니, Server Action 예제를 살펴보자.

- 먼저, Server Action을 사용하지 않는 방법으로 아이디와 비밀번호로 로그인 화면을 만들어보았다.
	- `nextauth`를 함께 사용하면, AuthOption으로 지정한 방법대로 로그인한 사용자의 세션 정보를 불러올 수 있을 것이다.
```tsx
"use client";

import { signIn } from "next-auth/react";
import { useState } from "react";
export default function Login2({ to }: { to?: boolean }) {
  const [id, setId] = useState<string>("");
  const [pw, setPw] = useState<string>("");

  return (
    <div
      style={{
        display: "flex",
        flexDirection: "column",
        padding: "5px",
        gap: "1rem",
      }}
    >
      <input
        value={id}
        style={{ width: "100%", height: "40px" }}
        onChange={(e) => {
          setId(e.currentTarget.value);
        }}
      />
      <input
        value={pw}
        style={{ width: "100%", height: "40px" }}
        onChange={(e) => {
          setPw(e.currentTarget.value);
        }}
      />

      <button
        style={{ width: "100px", height: "40px" }}
        onClick={() => {
          signIn("credentials", {
            userId: id,
            userPw: pw,
            redirect: false,
          }).then((res) => {
            window.location.href = "/";
          });
        }}
      >
        로그인
      </button>
    </div>
  );
}
```

- 서버 컴포넌트에서는, 로그인된 정보를 받아온 후, Client Component로 UserID를 전달한다.
	- nextauth에서는, 서버 컴포넌트 측에서 `getServerSession(...)` 메소드를 사용하면, Session 정보를 얻어올 수 있다.
```tsx
export const dynamic = "force-dynamic";

import { getServerSession } from "next-auth/next";
import ClientComponent from "./ClientComponent";
import { authOption } from "../api/auth/[...nextauth]/AuthOptions";

// Server Component
export default async function Page() {
  const session = await getServerSession(authOption);

  // 유저 정보 불러오기
  return (
    <ClientComponent
      userId={session && session.user ? session.user.userId : "null"}
    />
  );
}
```

- 클라이언트 컴포넌트에서는 Server Action 함수를 import하고, `<form>`태그를 추가해, 사용자 이벤트를 관리하고 있다.
```tsx
"use client";

import React from "react";
import { updateUser } from "../utils/action/actions";

const ClientComponent = ({ userId }: { userId: string | null }) => {
  if (!userId) {
    return null;
  }

  const updateUserWithId = updateUser.bind(null, userId);
  return (
    <form action={updateUserWithId}>
      <input type="text" name="userNm" />
      <input type="password" name="password" />
      <input type="text" name="age" />
      <button type="submit">Update User</button>
    </form>
  );
};

export default ClientComponent;
```

- `actions.ts`파일에 따로 저장한 `updateUser`는 아래와 같이 구성했다. 
	- 만약 ClientComponent에서 `action`이 실행되면 `console.log` 함수가 실행될 것이다.
```

```