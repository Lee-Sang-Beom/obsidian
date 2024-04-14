
#### 1. 원래 코드
```tsx
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
        const res = await fetch(
          `${process.env.NEXT_PUBLIC_HOST}/bapi/common/member/login`,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              userId: credentials?.userId,
              userPswd: credentials?.userPswd,
            }),
          }
        );
        const json = await res.json();

        if (json.code === "200") {
          // console.log("로그인 성공 : ", json.data);

          return json.data;
        } else {
          // err메시지 전달 : 서버 컴포넌트에서 getServerSession으로 받을
          // return JSON.stringify(json);
          throw new Error(JSON.stringify(json));
        }
      },
    }),
  ],
  // 인증 프로세스 중 다양한 시점에서 호출되는 함수
  callbacks: {
    // 서버한테서 로그인하고 get,post 하려면 로그인 시 token 받음
    async jwt({ token, user }: { token: JWT; user: User | AdapterUser }) {
      // authorize 메서드에서 반환된 사용자 객체 (로그인 시에만 제공됩니다)

      if (user) {
        token.user = user;
      }
      return token;
    },
    // JWT (JSON Web Token)의 생성과 검증을 사용자 정의하고 조작할 수 있게 합니다.
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
    // db 미지정 시, 자동으로 true가 되면서 next-auth에서 jwt를 사용하는 것으로 인지
    strategy: "jwt",
    maxAge: 8 * 60 * 60,
  },
  jwt: {
    secret: process.env.NEXTAUTH_SECRET,

    // 암호화를 사용하려면 true로 설정합니다. 기본값은 false(서명만)입니다.
    // encryption: true,
    // encryptionKey: "",
    // decryptionKey = encryptionKey,
    // decryptionOptions = {
    // algorithms: ['A256GCM']
    // },
  },
};
```

#### 2. 변경 (1) 코드: 차장님 도움없을 때
```tsx
import { AuthOptions, Session, User } from "next-auth";
import CredentialsProvider from "next-auth/providers/credentials";
import { AdapterUser } from "next-auth/adapters";
import { JWT } from "next-auth/jwt";

// AccessToken이 만료되면 refreshToken을 사용해서 다시 받아오는 함수
async function refreshAccessToken(token: JWT) {
  try {
    const currentTimeMillis = Date.now();
    const eightHoursMillis = 8 * 60 * 60 * 1000;
    const eightHoursLaterSeconds = Math.round(
      (currentTimeMillis + eightHoursMillis) / 1000
    );

    // 여기서 새거받아야함
    return {
      ...token,
      accessToken: token.user.jwtAuthToken,
      accessTokenExpires: eightHoursLaterSeconds,
      refreshToken: token.user.jwtRefreshToken ?? token.user.jwtAuthToken,
    };
  } catch (err) {
    return {
      ...token,
      error: "RefreshAccessTokenError",
    };
  }
}

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
        const res = await fetch(
          `${process.env.NEXT_PUBLIC_HOST}/bapi/common/member/login`,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              userId: credentials?.userId,
              userPswd: credentials?.userPswd,
            }),
          }
        );
        const json = await res.json();

        if (json.code === "200") {
          // console.log("로그인 성공 : ", json.data);

          return json.data;
        } else {
          // err메시지 전달 : 서버 컴포넌트에서 getServerSession으로 받을
          // return JSON.stringify(json);
          throw new Error(JSON.stringify(json));
        }
      },
    }),
  ],
  // 인증 프로세스 중 다양한 시점에서 호출되는 함수
  callbacks: {
    // 서버한테서 로그인하고 get,post 하려면 로그인 시 token 받음
    async jwt({ token, user }: { token: JWT; user: User | AdapterUser }) {
      // authorize 메서드에서 반환된 사용자 객체 (로그인 시에만 제공됩니다)

      // Initial sign in
      if (user) {
        const currentTimeMillis = Date.now();
        const eightHoursMillis = 8 * 60 * 60 * 1000;
        const eightHoursLaterSeconds = Math.round(
          (currentTimeMillis + eightHoursMillis) / 1000
        );

        token.user = user;
        token.accessToken = user.jwtAuthToken;

        // 만료 일자는 초단위로 계산
        //@ts-ignore
        token.accessTokenExpires = eightHoursLaterSeconds;

        token.refreshToken = user.jwtRefreshToken;

        return token;
      }

      // Return previous token if the access token has not expired yet
      // @ts-ignore
      if (Date.now() / 1000 < token.accessTokenExpires) {
        return token;
      }

      // Access token has expired, try to update it
      return refreshAccessToken(token);
    },
    // JWT (JSON Web Token)의 생성과 검증을 사용자 정의하고 조작할 수 있게 합니다.
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
    signOut: "/",
  },
  session: {
    // db 미지정 시, 자동으로 true가 되면서 next-auth에서 jwt를 사용하는 것으로 인지
    strategy: "jwt",
    maxAge: 8 * 60 * 60,
  },
  jwt: {
    secret: process.env.NEXTAUTH_SECRET,

    // 암호화를 사용하려면 true로 설정합니다. 기본값은 false(서명만)입니다.
    // encryption: true,
    // encryptionKey: "",
    // decryptionKey = encryptionKey,
    // decryptionOptions = {
    // algorithms: ['A256GCM']
    // },
  },
};

```

#### 3. 변경 (2) 코드: 차장님 도움 있을 때

```tsx
import { AuthOptions, Session, User } from "next-auth";
import CredentialsProvider from "next-auth/providers/credentials";
import { AdapterUser } from "next-auth/adapters";
import { JWT, decode } from "next-auth/jwt";

// AccessToken이 만료되면 refreshToken을 사용해서 다시 받아오는 함수
async function refreshAccessToken(token: JWT) {
  try {
    const currentTimeMillis = Date.now();
    const eightHoursMillis = 8 * 60 * 60 * 1000;
    const eightHoursLaterSeconds = Math.round(
      (currentTimeMillis + eightHoursMillis) / 1000
    );

    // 여기서 새거받아야함
    return {
      ...token,
      accessToken: token.user.jwtAuthToken,
      accessTokenExpires: eightHoursLaterSeconds,
      refreshToken: token.user.jwtRefreshToken ?? token.user.jwtAuthToken,
    };
  } catch (err) {
    return {
      ...token,
      error: "RefreshAccessTokenError",
    };
  }
}

async function rotateToken(token: JWT) {
  console.log("return token : ", token);
  return {
    ...token,
  };
  // try {
  //   const response = await fetch(
  //     process.env.BACKEND_URL + "/auth/token/access",
  //     {
  //       method: "POST",
  //       headers: {
  //         //@ts-ignore
  //         authorization: `Bearer ${token.refreshToken}`,
  //       },
  //     }
  //   );
  //   const refreshedTokenResponse = await response.json();
  //   if (refreshedTokenResponse.statusCode === 401) {
  //     return null;
  //     // return signout();
  //   }
  //   const decodeUser = decode(refreshedTokenResponse.accessToken);
  //   return {
  //     ...token,
  //     accessToken: refreshedTokenResponse.accessToken,
  //     //@ts-ignore
  //     accessTokenExpires: decodeUser?.exp * 1000,
  //   };
  // } catch (e) {
  //   console.error("Refresh token error", e);
  //   return {
  //     ...token,
  //     error: "RefreshAccessTokenError",
  //   };
  // }
}

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
        const res = await fetch(
          `${process.env.NEXT_PUBLIC_HOST}/bapi/common/member/login`,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              userId: credentials?.userId,
              userPswd: credentials?.userPswd,
            }),
          }
        );
        const json = await res.json();

        if (json.code === "200") {
          // console.log("로그인 성공 : ", json.data);

          return json.data;
        } else {
          // err메시지 전달 : 서버 컴포넌트에서 getServerSession으로 받을
          // return JSON.stringify(json);
          throw new Error(JSON.stringify(json));
        }
      },
    }),
  ],

  // 인증 프로세스 중 다양한 시점에서 호출되는 함수
  callbacks: {
    //@ts-ignore
    async jwt({ token, user }: { token: JWT; user: User | AdapterUser }) {
      // Initial sign in
      if (user) {
        const currentTimeMillis = Date.now() / 1000;
        const eightHoursMillis = 8 * 60 * 60;
        const eightHoursLaterSeconds = currentTimeMillis + eightHoursMillis;

        token.user = user;
        token.accessToken = user.jwtAuthToken;
        token.refreshToken = user.jwtRefreshToken;

        //@ts-ignore
        token.accessTokenExpires = eightHoursLaterSeconds; // 만료 일자를 초로 계산 (전부)

        return token;
      }

      // Date.now()로 구한 현재 시간(밀리초)이 주어진 토큰의 접근 토큰 만료 시간(token.accessTokenExpires)보다 클 때를 검사
      // 현재 시간이 접근 토큰의 만료 시간을 초과했는지 검사

      // @ts-ignore
      if (Date.now() > token.accessTokenExpires) {
        //@ts-ignore
        const newToken = await rotateToken(token);
        if (newToken === null)
          return {
            error: "refreshTokenExpired",
          };
        //@ts-ignore
        token = newToken;
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
    signOut: "/",
  },
  session: {
    // db 미지정 시, 자동으로 true가 되면서 next-auth에서 jwt를 사용하는 것으로 인지
    strategy: "jwt",
    maxAge: 8 * 60 * 60,
  },
  jwt: {
    secret: process.env.NEXTAUTH_SECRET,

    // 암호화를 사용하려면 true로 설정합니다. 기본값은 false(서명만)입니다.
    // encryption: true,
    // encryptionKey: "",
    // decryptionKey = encryptionKey,
    // decryptionOptions = {
    // algorithms: ['A256GCM']
    // },
  },
};

```