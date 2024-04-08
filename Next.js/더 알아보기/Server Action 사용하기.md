
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


#### 3. 규칙

- ***Server Action***은 React의 `"use server"` 지시문을 사용하여 정의될 수 있다. 
	- 이 지시문을 비동기 함수의 맨 위에 배치하면, 해당 함수가 **Server Action으로 지정된 함수다!** 라고 표시할 수 있다.
	- 혹은, 별도의 파일의 맨 위에 배치하여 해당 파일의 모든 내보내기를 서버 액션으로 표시할 수 있습니다.

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