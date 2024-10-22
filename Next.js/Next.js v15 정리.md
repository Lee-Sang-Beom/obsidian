
### 1. 소개 및 업데이트 방법

- Next.js 15가 공식적으로 안정화되었고 프로덕션 환경에서 사용할 준비가 되었다고 한다.
	- 이번 릴리스는 RC1과 RC2에서의 업데이트를 기반으로 하며, 안정성에 중점을 두면서도 흥미로운 업데이트를 추가했다고 하니 그 내용을 살펴보자.

> 자동 업그레이드 CLI
```bash
npx @next/codemod@canary upgrade latest
```

> 수동 업그레이드 CLI
```bash
npm install next@latest react@rc react-dom@rc
```


### 2. 새로운 기능

- [**`@next/codemod` CLI:**](https://nextjs.org/blog/next-15#smooth-upgrades-with-nextcodemod-cli) 최신 Next.js와 React 버전으로 쉽게 업그레이드할 수 있습니다.
- [**Async Request APIs (Breaking):**](https://nextjs.org/blog/next-15#async-request-apis-breaking-change) 단순화된 렌더링 및 캐싱 모델을 위한 점진적 단계입니다
- [**Caching Semantics (Breaking):**](https://nextjs.org/blog/next-15#caching-semantics) `fetch` 요청, GET 라우트 핸들러, 클라이언트 탐색이 기본적으로 캐시되지 않습니다.
- [**React 19 Support:**](https://nextjs.org/blog/next-15#react-19) React 19, React Compiler (실험적) 및 수명 오류 개선을 지원합니다
- [**Turbopack Dev (Stable):**](https://nextjs.org/blog/next-15#turbopack-dev) 성능 및 안정성 개선.
- [**Static Indicator:**](https://nextjs.org/blog/next-15#static-route-indicator) 개발 중 정적 경로를 보여주는 새로운 시각적 표시기.
- [**`unstable_after` API (Experimental):**](https://nextjs.org/blog/next-15#executing-code-after-a-response-with-unstable_after-experimental) 응답 스트리밍이 끝난 후 코드를 실행합니다.
- [**`instrumentation.js` API (Stable):**](https://nextjs.org/blog/next-15#instrumentationjs-stable) 서버 생명 주기 관찰을 위한 새로운 API.
- [**Enhanced Forms (`next/form`):**](https://nextjs.org/blog/next-15#form-component) **클라이언트 측 탐색으로 HTML 폼을 향상시킵니다.**
- [**`next.config`:**](https://nextjs.org/blog/next-15#support-for-nextconfigts) TypeScript 지원을 위한 next.config.ts 지원.
- [**Self-hosting Improvements:**](https://nextjs.org/blog/next-15#improvements-for-self-hosting) Cache-Control 헤더에 대한 더 많은 제어
- [**Server Actions Security:**](https://nextjs.org/blog/next-15#enhanced-security-for-server-actions) 예측할 수 없는 엔드포인트와 사용하지 않는 액션 제거.
- [**Bundling External Packages (Stable):**](https://nextjs.org/blog/next-15#optimizing-bundling-of-external-packages-stable) App 및 Pages Router에 대한 새로운 구성 옵션.
- [**ESLint 9 Support:**](https://nextjs.org/blog/next-15#eslint-9-support) ESLint 9에 대한 지원 추가.
- [**Development and Build Performance:**](https://nextjs.org/blog/next-15#development-and-build-improvements) 빌드 시간 개선 및 더 빠른 빠른 새로 고침.


### 3. @next/codemod CLI를 통한 원활한 업그레이드

- 모든 주요 Next.js 릴리스마다 코드 변환을 자동화하는 코드를 포함하여 깨진 변경 사항을 업그레이드하는 데 도움을 줄 수 있도록 하기 위해, 업그레이드를 더 매끄럽게 하기 위해 향상된 codemod CLI가 출시되었다고 한다.

```bash
npx @next/codemod@canary upgrade latest
```

- 이 도구는 코드베이스를 최신 안정 버전 또는 프리릴리스 버전으로 업그레이드하는 데 도움을 준다.
	- CLI는 종속성을 업데이트하고, 사용 가능한 codemods를 보여주며, 이를 적용하는 방법을 안내합니다.

- canary 태그는 최신 버전의 codemod를 사용하고, latest는 Next.js 버전을 지정할 수 있도록 하는 키워드이다.
	- 최신 Next.js 버전으로 업그레이드하더라도 codemod의 canary 버전을 사용하는 것을 추천하며, 사용자 피드백에 따라 도구의 개선 사항을 지속적으로 추가할 계획이라고 한다.


### 4. 비동기 요청 API (Breaking Change)

- 전통적인 서버 사이드 렌더링에서는 서버가 요청을 기다린 후에야 콘텐츠를 렌더링한다.
	- 그러나 모든 컴포넌트가 요청 특정 데이터에 의존하는 것은 아니므로, 이러한 컴포넌트를 렌더링하기 위해 요청을 기다릴 필요는 없다. 
	- 이상적으로는 서버가 요청이 도착하기 전에 가능한 한 많은 작업을 준비할 수 있어야 한다.
	- 이를 가능케하고 향후 최적화를 위한 기초를 마련하기 위해서는, **요청을 기다려야 할 시점을 알고 있어야 한다.**

- 따라서 헤더, 쿠키, 파라미터 및 searchParams와 같은 요청 특정 데이터에 의존하는 API를 비동기적으로 전환하고 있다고 한다.

> 기존 `cookies()` 사용 예시
```js
const found = cookies().get(cookieName);
```

> 변경된 `cookies()` 사용 예시
```js
import { cookies } from 'next/headers';

export async function AdminPanel() {
  const cookieStore = await cookies();
  const token = cookieStore.get('token');

  // ...
}
```

- 더욱 원활한 **마이그레이션**(데이터나 소프트웨어를 한 시스템에서 다른 시스템으로 이동하는 것)을 위해 이러한 API는 일시적으로 동기적으로 접근할 수 있지만, 다음 주요 버전까지는 개발 및 프로덕션 환경에서 경고가 표시된다고 한다.
	- 마이그레이션을 자동화하기 위한 코드는 아래와 같다.
```bash
npx @next/codemod@canary next-async-request-api .
```


### 5. 캐싱 의미론

- Next.js App Router는 특정한 캐싱 기본값과 함께 시작되었는데, 이는 기본적으로 가장 성능이 뛰어난 옵션을 제공하도록 설계되었으며, 필요 시 선택적으로 사용하지 않을 수 있었다.
	- 그런데, 이제는 사용자의 피드백을 바탕으로, 캐싱 히스토리와 Partial Prerendering (PPR) 프로젝트 및 `fetch`를 사용하는 타사 라이브러리와의 상호 작용을 재평가했다고 한다.

##### 5-1. [`fetch` Requests are no longer cached by default](https://nextjs.org/blog/next-15#fetch-requests-are-no-longer-cached-by-default)
- 이제, Next.js 15에서는 `fetch` 요청, GET Route Handlers, 클라이언트 라우터 캐시의 기본 캐싱 설정을 "기본적으로 캐시됨"에서 **"기본적으로 캐시되지 않음"** 으로 변경했다. 
	- 물론 이전 동작을 유지하려면 캐싱을 계속 선택적으로 사용할 수 있다고 한다.

```js
fetch('https://...', { cache: 'force-cache' | 'no-store' });
```
- fetch 요청의 `cache` 옵션 내용은 아래와 같다.
	- **no-store**: 매 요청 시 원격 서버에서 자원을 가져오고 캐시는 업데이트하지 않음
	- **force-cache**: 캐시에서 자원을 가져오거나 원격 서버에서 가져와 캐시를 업데이트함

- Next.js 14에서는 캐시 옵션이 제공되지 않은 경우 기본적으로 force-cache가 사용되었으나, 동적 함수나 동적 구성 옵션이 사용된 경우는 제외되었다.
	- 이제 **Next.js 15에서는 캐시 옵션이 제공되지 않은 경우 기본적으로 `no-store`가 사용된다.** 즉, fetch 요청은 기본적으로 캐시되지 않는다.

- 캐시 요청을 계속 사용하려면 다음과 같이 할 수 있다고 한다.
	- 단일 fetch 호출에서 캐시 옵션을 `force-cache`로 설정
	- 단일 라우트에 대해 `dynamic route config` 옵션을 `'force-static'`으로 [설정](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config#dynamic)
	- Layout 또는 Page에서 모든 `fetch` 요청이 `force-cache`를 사용하도록 fetchCache route config 옵션을 `'default-cache'`로 [설정](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config#fetchcache) (명시적으로 자신의 캐시 옵션을 지정하지 않는 경우)

##### 5-2. [`GET` Route Handlers are no longer cached by default](https://nextjs.org/blog/next-15#get-route-handlers-are-no-longer-cached-by-default)
- Next.js 14에서는 GET HTTP 메서드를 사용하는 Route Handlers가 기본적으로 캐시되었다.
	- 그러나 Next.js 15에서는 GET 함수가 기본적으로 캐시되지 않는다.
	- 여전히 static route config 옵션을 사용하여 캐싱을 선택할 수 있다. (예 : `export dynamic = 'force-static'`)

- 하지만, `sitemap.ts`, `opengraph-image.tsx`, `icon.tsx`와 같은 특별한 Route Handlers 및 기타 메타데이터 파일은 동적 함수나 동적 구성 옵션을 사용하지 않는 한 기본적으로 정적이라 한다.

##### 5-3.  [Client Router Cache no longer caches Page components by default](https://nextjs.org/blog/next-15#client-router-cache-no-longer-caches-page-components-by-default)

- Next.js v14.2.0에서는 Router Cache의 사용자 정의 구성을 허용하기 위해 실험적인 `staleTimes` 플래그를 도입했었다.
	- **클라이언트 측 route cache**는 방문한 경로와 미리 가져온 경로를 클라이언트에 캐시하여, 빠른 네비게이션 경험을 제공하기 위해 설계된 캐싱 레이어이다.
		- 커뮤니티 피드백을 기반으로, Next.js v14.2에서는 experimental `staleTimes` 옵션을 추가하여 클라이언트 측의 router cache의 무효화 기간을 구성할 수 있도록 했다.
	
	- 기본적으로, `prefetch prop`을 사용하지 않고 `<Link>` 컴포넌트를 통해 미리 가져온 경로는 30초 동안 캐시되며, `prefetch prop`이 `true`로 설정된 경우에는 5분 동안 캐시된다. 
		- `next.config.js`에서는 사용자 정의 **재유효화 시간을 정의**하여 이러한 기본값을 덮어쓸 수 있다.
```ts
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    staleTimes: {
      dynamic: 30, // <Link>에서 prefetch prop이 지정되지 않은 경우에 사용된다. 기본값: 30초
      static: 180, // <Link>에서 prefetch prop이 true로 설정되거나 router.prefetch를 호출한 경우에 사용된다. (기본값 5분)
    },
  },
}

module.exports = nextConfig
```
- 위 예제에 대한 설명은 아래와 같다.
	1. `prefetch prop`가 지정되지 않은 `<Link>`에서는 30초동안 캐시된 페이지를 빠르게 확인할 수 있다.
	2. `prefetch prop`이 `true`로 설정되거나, `router.prefetch`를 호출한 경우는 180초동안 캐시된 페이지를 빠르게 확인할 수 있다.

- Next.js 15에서는 이 플래그는 여전히 사용할 수 있지만, 기본 동작을 Page 세그먼트에 대해 staleTime을 0으로 변경했다고 한다. 
	- 즉, 애플리케이션을 탐색하는 동안 클라이언트는 탐색의 일환으로 활성화된 Page 컴포넌트에서 항상 최신 데이터를 가져와 페이지에 반영할 수 있도록 한다는 뜻이다. 

- Next.js 15에서도 변경되지 않은 **예외사항**은 아래와 같다.
	- 공유 레이아웃 데이터는 부분 렌더링을 지원하기 위해 서버에서 다시 가져오지 않음.
	- 뒤로/앞으로 탐색은 캐시에서 복원되어 브라우저가 스크롤 위치를 복원할 수 있도록 보장함.
	- `loading.js`는 5분 동안 캐시됨 (또는 staleTimes.static 구성의 값).


### 6. React 19

- Next.js15에서 가장 크게 변화가 있었던 내용 중 하나라고 생각하는 부분인데, Next.js 15는 React 19와의 호환성을 맞추기로 결정했다.
	- v15에서는 App Router가 React 19 RC를 사용하며, 커뮤니티 피드백을 바탕으로 Pages Router에 대한 React 18의 하위 호환성을 도입했다.

- 비록 React 19가 **아직 RC 단계**에 있지만, 실제 애플리케이션에서의 광범위한 테스트와 React 팀과의 긴밀한 협력이 그 안정성에 대한 확신을 주었다고 한다. 핵심 Breaking Change는 충분히 테스트되어 기존 App Router 사용자에게 영향을 미치지 않기 때문에, React 19 GA에 완전히 준비된 상태로 프로젝트를 진행할 수 있도록 지금 Next.js 15를 `stable` 버전으로 출시하기로 결정했다고 한다.
	- 원활한 마이그레이션 과정을 돕기 위한 `codemods`와 자동화 도구를 제공했다고 한다. (맨 처음 내용)


### 7. React 18의 Pages Router

- Next.js 15는 React 18에 대한 Pages Router의 **하위 호환성**을 유지하여 사용자가 React 18을 계속 사용할 수 있도록 하면서도 Next.js 15의 개선 사항을 누릴 수 있게 한다.
	- 첫 번째 Release Candidate (RC1) 이후, 커뮤니티 피드백을 바탕으로 React 18 지원을 포함할 수 있도록 포커싱했으며, 이러한 유연성 덕분에 React 18을 사용하는 Pages Router와 함께 Next.js 15를 채택할 수 있어 업그레이드 경로에 대한 더 큰 제어권을 제공할 수 있다고 한다.

- **참고**로, React 18에서 Pages Router를 실행하고 React 19에서 App Router를 실행하는 것이 가능하지만, 이 설정을 권장하지 않는데, 이러한 구성이 예측할 수 없는 동작 및 타입 불일치를 초래할 수 있기 때문이라 한다. (두 버전 간의 기본 API와 렌더링 논리가 완전히 일치하지 않을 수 있다고 함)


### 8. React 컴파일러 (실험단계)

- React 컴파일러는 Meta의 React 팀에 의해 만들어진 새로운 실험적 컴파일러이다.
	- 이 컴파일러는 일반 JavaScript 의미론과 React의 규칙을 이해하여 코드에 대한 깊은 이해를 바탕으로 자동 최적화를 추가할 수 있다.
	- 이 컴파일러는 개발자가 useMemo 및 useCallback과 같은 API를 통해 수동으로 **Memorization**을 수행해야 하는 양을 줄여주어, 코드가 더 간단하고 유지 관리하기 쉬우며 오류 발생 가능성을 줄여준다.

- Next.js 15에서는 [React 컴파일러](https://react.dev/learn/react-compiler)에 대한 지원을 추가했다고 하며, 현재 Babel 플러그인으로만 제공한다고 한다.


### 9. Hydration 오류 개선

- Next.js 14.1에서는 에러 메시지와 hydration 오류에 대한 개선이 이루어졌다. 
	- Next.js 15에서는 이러한 개선을 더욱 발전시켜 **향상된 hydration 오류 View**를 추가했다.
	- 이제 hydration 오류는 오류의 원본 소스 코드를 표시하고, 문제를 해결할 수 있는 제안도 함께 제공한다.

- 이제 hydration 오류가 발생하면, 에러의 근본적인 원인을 파악하고 해결 방법을 바로 확인할 수 있어 개발자 경험이 향상될 것이다.

> **Next.js 14.1 Hydration Error Message** (이미지 출처 : https://nextjs.org/blog/next-15)
![[hydration-error-before-dark.avif]]

> **Next.js 15 Hydration Error Message** (이미지 출처 : https://nextjs.org/blog/next-15)
![[hydration-error-after-dark.avif]]


### 10. Turbopack Dev

https://nextjs.org/blog/next-15