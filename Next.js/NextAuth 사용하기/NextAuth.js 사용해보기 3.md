
#### 1. User Type 재정의하기

- nextauth에서 관리되어야 할 User의 타입을 사용자가 원하는대로 재정의하기 위해서는 
```tsx

// 파일경로: types/nextAuth/next-auth.ts

import NextAuth from "next-auth";

interface AuthListType {
  authrtNm: string;
  authrtExpln: string;
}

// next-auth 모듈의 기본 타입을 확장한다.
// NextAuth에서 제공하는 `User` 및 `Session` type을 확장한다.
declare module "next-auth" {
  interface User {
    userSeq: number;
    userNm: string;
    userBirth: string | null;
    userTelno: string;
    userSxdcEnu: string | null;
    userId: string;
    userEmail: string;
    userTypeEnu: {
      type: "NORMAL" | "ADMIN";
      name: string;
    };
    authList: AuthListType[];
    jwtAuthToken: string;
    jwtRefreshToken: string;
    cmsAdminUserSeq: string;
    joinApprovalYn: "Y" | "N";

    // 오류메시지: 오류일때만 내용이 담겨서 온다.
    code?: string;
    description?: string;
    detail?: string;
  }
  
  interface Session {
    user: User;
  }
}

// JWT (JSON Web Token) 모듈에서 사용되는 `JWT` 인터페이스를 확장한다.
// 확장한 `JWT` 인터페이스는 `user` 필드를 가지며, 이 필드는 위에서 정의한 `User` 타입을 가지게 된다.
declare module "next-auth/jwt" {
  interface JWT {
    user: User;
  }
}

```