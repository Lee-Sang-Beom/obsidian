#### Next.js 설치

- NextAuth.js 라이브러리를 사용하기 위해서는 먼저, Next.js 애플리케이션 프로젝트를 만들어야 한다.

  - `npx create-next-app@latest`
  - 설치가 완료되면, `npm run dev` 명령어를 입력하여, 개발 서버가 정상적으로 실행되는지를 확인하자.

- 다음으로, NextAuth.js 라이브러리를 설치한다.
  - `npm install next-auth`

#### 프로젝트 세팅 (SessionProvider)

- NextAuth.js에서 제공하는 `useSession()`을 사용하기 위해서는, 애플리케이션 최상단에 `SessionProvider`로 하위 요소들을 감싸줘야 한다.

  - `useSession` : 클라이언트 컴포넌트에서 session 정보를 불러올 수 있는 `hook`

- Next.js 13을 사용하면서, 필자는 이를 `layout.tsx` 파일에서 사용하고 싶었기 때문에, 아래의 방법을 사용했다. - 프로젝트 디렉터리에 `/provider/NextAuthSessionProvider.tsx`라는 파일을 만든 후, 아래의 코드를 입력한다.
  ```tsx
  "use client";
  import React from "react";
  import { SessionProvider } from "next-auth/react";

export default function NextAuthSessionProvider({
children,
}: {
children: React.ReactNode;
}) {
return <SessionProvider>{children}</SessionProvider>;
}
```

- 그 다음, 앞서 정의한 `NextAuthSessionProvider`컴포넌트로 `app/layout.tsx` 파일의 `{children}` 영역을 감싸준다.

```tsx
import NextAuthSessionProvider from "@/provider/NextAuthSessionProvider";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="ko">
            <head>{/* ... */}</head>      <body>
                <NextAuthSessionProvider>{children}</NextAuthSessionProvider>   
         {" "}
      </body>   {" "}
    </html>
  );
}
```

#### 프로젝트 세팅 (route)

- Next.js 애플리케이션에서 NextAuth.js를 사용하기 위해서는, `/app/api/auth` 라는 약속된 경로 하위에 `[...nextauth].js`라는 파일을 만들어야 한다.

  - 이는, `/api/auth/*` 경로로 들어오는 모든 요청을 NextAuth.js에 의해 자동으로 처리되도록 하기 위해서이다.
  - 요청의 예시로는 `signIn, signOut, callback, 기타`) 등이 있는데, 이것들이 바로 NextAuth.js 에서 제공하고 있는 함수이다.

- NextAuth.js의 [공식문서](https://next-auth.js.org/getting-started/example)에는 아래와 같은 소스코드를 제공하고 있다.

```tsx
import NextAuth from "next-auth";
import GithubProvider from "next-auth/providers/github";

export const authOptions = {
  // Configure one or more authentication providers
  providers: [
    GithubProvider({
      clientId: process.env.GITHUB_ID,
      clientSecret: process.env.GITHUB_SECRET,
    }),
    // ...add more providers here
  ],
};

export default NextAuth(authOptions);
```

- 필자의 경우, `/api/auth/[...nextauth]`경로 하위에 `route.ts`와 `AuthOptions.ts`라는 파일을 만들어 사용하였다.

```tsx
// route.ts

import NextAuth from "next-auth";
import { authOptions } from "@/app/api/auth/[...nextauth]/AuthOptions";

const handler = NextAuth(authOptions);

export { handler as GET, handler as POST };
```

```tsx
// AuthOptions.ts

import { AuthOptions, Session, User } from "next-auth";
import CredentialsProvider from "next-auth/providers/credentials";
import { AdapterUser } from "next-auth/adapters";
import { JWT } from "next-auth/jwt";

export const authOptions: AuthOptions = {
  providers: [
    CredentialsProvider({
      name: "Normal",
      credentials: {
        userId: { label: "userId", type: "text" },
        userPswd: { label: "userPswd", type: "password" },
      },
      async authorize(
        credentials: Record<"userId" | "userPswd", string> | undefined
      ) {
        const res = await fetch(`로그인 API 주소`, {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            userId: credentials?.userId,
            userPswd: credentials?.userPswd,
          }),
        });
        const json = await res.json();

        if (json.code === "200") {
          return json.data;
        } else {
          // err메시지 전달 : 서버 컴포넌트에서 getServerSession으로 받을
          // return JSON.stringify(json);
          throw new Error(JSON.stringify(json));
        }
      },
    }),
  ],

  callbacks: {
    async jwt({ token, user }: { token: JWT; user: User | AdapterUser }) {
      if (user) {
        token.user = user;
      }
      return token;
    },

    async session({ session, token }: { session: Session; token: JWT }) {
      session.user = token.user;
      return session;
    },

    async redirect({ url, baseUrl }: { url: string; baseUrl: string }) {
      if (!url.includes("http")) {
        return "http://localhost:3000" + url;
      }
      return url;
    },
  },

  pages: {
    signIn: "/",
  },
  session: {
    strategy: "jwt",
    maxAge: 8 * 60 * 60,
  },
};
```
