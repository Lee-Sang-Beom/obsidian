### 1. Overview

 - [Next.js14](https://nextjs.org/blog/next-14#nextjs-learn-course)버전에 대한 업데이트 내용을 보던 중 **server-action(stable)** 이라는 내용이 문득 눈에 띄었다. 평소 써보지 않았던 기능이기 때문에, 이게 뭔지 참 궁금했다.
 
 - 이 참에 사용해보면서 해당 기능에 대해 알아보고, 거기다 기록까지 해 두면 금상첨화일 것 같아 포스트를 작성하게 되었다.

- [참고링크](https://velog.io/@ckstn0777/Next.js-13.4-Server-Actions%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C)

```tsx

> next.config.js

const path = require("path");
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    serverActions: true, // 추가
  }, 
  sassOptions: {
    includePaths: [path.join(__dirname, "styles")],
  },
  output: "standalone",
};
module.exports = nextConfig;
```

```tsx
> Test.ts

export async function handleSubmit(formData: any) {
  "use server";
  console.log(formData);
  console.log(formData.get("title"));
}
```

```tsx

> Test2.tsx

"use client";
import { useState } from "react";
export default function Test2({ handleSubmit }: any) {
  const [text, setText] = useState<string>("hello");

  return (
    <form action={handleSubmit}>
      <input
        type="text"
        name="title"
        value={text}
        onChange={(e) => {
          setText(e.currentTarget.value.toString());
        }}
        style={{ border: "1px solid black", padding: "5px" }}
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

```tsx

> Page.tsx
>
import { handleSubmit } from "./Test";
import Test2 from "./Test2";

  

export default async function comModiPage() {

  // const session = await getServerSession(authOptions);

  // if (

  //   session === undefined ||

  //   session == null ||

  //   session.user == null ||

  //   session.user.userSeq == null ||

  //   !session.user.userSeq

  // ) {

  //   // 세션에 유저 정보가 없으면? (로그인되지 않음)

  //   redirect("/virtual");

  // }

  // return <ComModi user={session.user} />;

  return <Test2 handleSubmit={handleSubmit} />;

}
```