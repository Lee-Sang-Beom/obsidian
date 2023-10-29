
##### Next.js 설치

- NextAuth.js 라이브러리를 사용하기 위해서는 먼저, Next.js 애플리케이션 프로젝트를 만들어야 한다. 
	- `npx create-next-app@latest` 
	- 설치가 완료되면, `npm run dev` 명령어를 입력하여, 개발 서버가 정상적으로 실행되는지를 확인하자.

- 다음으로, NextAuth.js 라이브러리를 설치한다. 
	- `npm install next-auth`


##### 프로젝트 세팅

- 프로젝트에서 NextAuth.js를 사용하기 위해서는, `/app/api/auth` 라는 약속된 경로 하위에 `[...nextauth].js`라는 파일을 만들어야 한다.
	- 이는, `/api/auth/*` 경로로 들어오는 모든 요청을 NextAuth.js에 의해 자동으로 처리되도록 하기 위해서이다.
	- 요청의 예시로는 `signIn signOut, callback, 기타`) 등이 있다.

- NextAuth.js의 [공식문서](https://next-auth.js.org/getting-started/example)에는 아래와 같은 소스코드를 제공하고 있다.
```tsx
import NextAuth from "next-auth"
import GithubProvider from "next-auth/providers/github"

export const authOptions = {
  // Configure one or more authentication providers
  providers: [
    GithubProvider({
      clientId: process.env.GITHUB_ID,
      clientSecret: process.env.GITHUB_SECRET,
    }),
    // ...add more providers here
  ],
}

export default NextAuth(authOptions)
```

