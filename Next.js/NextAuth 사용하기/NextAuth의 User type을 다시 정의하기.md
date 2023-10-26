
```tsx

import NextAuth from "next-auth";

interface AuthListType {
  authrtNm: string;
  authrtExpln: string;
}


// next-auth에서 
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

    // 오류메시지: 오류일때만 내용이 담겨서 옴
    code?: string;
    description?: string;
    detail?: string;
  }
  
  interface Session {
    user: User;
  }
}

declare module "next-auth/jwt" {
  interface JWT {
    user: User;
  }
}

```