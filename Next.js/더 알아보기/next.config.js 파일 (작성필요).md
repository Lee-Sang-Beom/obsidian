
#### 1. next.config.js

- `next.config.js` 파일은 Next.js 프로젝트에서, 추가적인 설정을 할 수 있도록 하는 **Node.js 모듈**이다.
	- 해당 파일은 Next.js의 서버 및 빌드 단계에서 사용된다. (브라우저 빌드에서는 포함되지 않는다.)

- `next.config.js`의 설정은 모두 '옵션'이다. 이는 필수가 아니라는 뜻이다.
	- 따라서, 필요한 설정만 찾아 변경을 하는 것이 추천된다.

- 아래에서는, 간단한 예시를 들고자 한다.

#### 2. redirects

```js
const nextConfig = {
  async redirects() {
    return [
      {
	    // source: "/sub/:path*"
        source: "/sub",
        destination: "/",
        permanent: true,
      },
    ];
  },
}
```

- next.config.js의 **redirects** 키를 사용하면, 들어오는 **request(요청) 경로**를 다른 **destination(목적지 - target)** 경로로 redirect시킬 수 있다.
	- 이름 그대로, redirect 시키기 때문에 **페이지 내용과 URL 모두 변경**된다.
	- 위 예시는, 사용자가 `/sub`로 페이지 경로 요청 및 진입을 시도하면 `/` 경로로 redirect시키는 코드이다.

> **Option**
- `source` : 사용자 접근 요청 경로 패턴
	- 위 예제에서, 주석화된 `/sub/:path*` 코드는 sub 경로 및 sub 하위의 모든 경로를 request(요청) 경로로 간주한다는 의미이다.
- `destination` : `source`에 맞는 패턴 경로로 들어온 사용자들을 redirect시킬 경로
- `permanent(boolean)` 
	- `true`일 경우, 클라이언트/검색 엔진에 redirect을 영구적으로 캐시하도록 지시하는 308 상태 코드를 사용한다.
	- `false`일 경우, 307 임시 상태 코드를 사용하여, 캐시되지 않도록 한다.


#### 3. rewrites

https://velog.io/@ahuuae/nextconfigjs-redirects-rewrites#:~:text=next.config.js%EB%8A%94%20%ED%8C%8C%EC%9D%BC%EB%AA%85,%EB%B9%8C%EB%93%9C%EC%97%90%EC%84%9C%EB%8A%94%20%ED%8F%AC%ED%95%A8%EB%90%98%EC%A7%80%20%EC%95%8A%EB%8A%94%EB%8B%A4.