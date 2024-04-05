
#### 작업

1. `api` 주소문자열을 감싸고, 실제 요청될 API BaseURL을 반환하는 함수를 만들고, 모든 클라이언트 요청 영역 적용
	- 단, (`serversidefetch` 제외)
	- `main`: `cwchemical` 추가
	- `dev, localhost`: `cwchemical` 추가


2. 전체 디렉터리 구조 변경 `root` 하위 `cwchemical` 추가
	- `app`
	- `public`

3. `sessionProvider` `basepath`변경
```tsx
"use client";

import React, { use, useEffect, useLayoutEffect, useState } from "react";
import { SessionProvider } from "next-auth/react";

export default function NextAuthSessionProvider({
  children,
}: {
  children: React.ReactNode;
}) {
  // dev환경일 때 : /cwchemical/api/auth
  // local환경일 때 : /api/auth
  const [basePath, setBasePath] = useState<string>("/cwchemical/api/auth");
  useLayoutEffect(() => {
    if (window) {
      if (
        window.location.href.includes("localhost") ||
        window.location.href.includes("http://cca.lksmart.co.kr")
      )
        setBasePath("/api/auth");
    }
  }, []);

  return <SessionProvider basePath={basePath}>{children}</SessionProvider>;
}
```

4. `dev` branch: `assetPrefix` 제거
```ts
const path = require("path");

/** @type {import('next').NextConfig} */
const nextConfig = {
  // experimental: {
  //   appDir: true,
  // },
  sassOptions: {
    includePaths: [path.join(__dirname, "styles")],
  },
  images: {
    domains: ["http://cca.lksmart.co.kr"],
    remotePatterns: [
      {
        protocol: "http",
        hostname: "localhost",
      },
      {
        protocol: "http",
        hostname: "cca.lksmart.co.kr",
      },
      {
        protocol: "https",
        hostname: "cca.lksmart.co.kr",
      },
    ],
  },

  // 라이브에서만 추가
  // basePath: "/cwchemical",
  // assetPrefix: "/cwchemical/",

  output: "standalone",
};

module.exports = nextConfig;

```
6. `api key (vworld)` 변경
7. 로그아 로직 변경


[중요]
#### 완료
1. sessionProvider basePath 변경
2. app 하위요소 및 이미지 접근경로 cwchemical 추가
3. 로컬 및 dev환경 `assertPrefix` 삭제
4. `api key`