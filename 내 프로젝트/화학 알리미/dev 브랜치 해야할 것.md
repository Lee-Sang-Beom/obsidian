
#### 1. 정적파일 경로

- `next-config.js` 파일 중 아래의 내용이 있다. (라이브임)
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
  assetPrefix: "/cwchemical/",

  output: "standalone",
};

module.exports = nextConfig;
```

- 여기서 `assetPrefix`는 