
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
	- 원활한 마이그레이션(데이터나 소프트웨어를 한 시스템에서 다른 시스템으로 이동하는 것) 과정을 돕기 위한 `codemods`와 자동화 도구를 제공했다고 한다. (맨 처음 내용)


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


### 10. Turbopack Dev (Turbopack Dev 안정화 및 성능 개선)

- Next.js의 `next dev --turbo` 명령이 안정화되어, 이제 개발 과정에서 성능을 대폭 개선할 수 있다.
	- 이 기능을 Vercel의 여러 애플리케이션에서 이미 사용하고 있으며, 특히 Vercel의 대형 Next.js 앱인 `vercel.com`에서 다음과 같은 성능 향상을 얻었다고 한다.
		- **로컬 서버 시작 속도**: 최대 76.7% 더 빨라졌음
		- **빠른 업데이트 속도 (Fast Refresh)**: 최대 96.3% 더 빠르게 코드 업데이트가 반영됨
		- **초기 경로 컴파일 속도**: 최대 45.8% 더 빨라졌음 (단, Turbopack은 아직 디스크 캐시를 사용하지 않음)

- 자세한 내용은 Turbopack Dev 관련 블로그 포스트에서 확인할 수 있습니다.


### 11. Static Route Indicator

- 개발 중에 **Static Route Indicator**라는 시각적 표시가 추가되어, 정적 라우트와 동적 라우트를 쉽게 구분할 수 있게 되었다. 이를 통해 페이지의 렌더링 방식을 이해하고, 성능을 최적화할 수 있다. 

- 또한 `next build` 출력을 통해 모든 경로의 렌더링 전략을 확인할 수도 있다.

- 이 업데이트는 Next.js에서 가시성을 높이기 위한 노력의 일환으로, 개발자가 애플리케이션을 모니터링, 디버깅, 최적화하는 데 도움을 주기 위해 제공되었다. 더불어 향후 개발자 도구도 추가될 예정이라고 한다.


### 12. Executing code after a response with `unstable_after` (Experimental)

- `unstable_after`는 사용자 응답 후에 비동기 작업을 수행할 수 있도록 해주는 기능이다. 이 API는 특히 응답과 직접 관련이 없는 **로그 기록, 분석, 외부 시스템 동기화** 같은 작업을 사용자가 기다리지 않게 할 때 유용하다.

- 기존 서버리스 함수는 응답이 완료되면 즉시 작업을 중지하므로, 응답 이후에 작업을 처리하는 데 어려움이 있었다. `unstable_after`는 이러한 문제를 해결하여 응답이 완료된 후에 **추가 작업을 예약**할 수 있게 해 준다. 이로써 응답 후에 실행할 보조 작업들이 **주 응답을 차단하지 않고** 수행할 수 있다.

- 이 방식은 응답 성능을 향상시키고, 응답 이후 실행할 작업(예: 로그 기록, 외부 API 호출 등)을 **사용자가 기다리지 않게 처리**할 수 있도록 도와줍니다. `after`를 사용해 응답 후 비동기적으로 실행되는 작업을 설정하면, 사용자 경험을 저하시키지 않으면서 필요한 작업을 실행할 수 있다.

##### ※ 사용 방법
- **`next.config.js` 설정**: `next.config.js`에 아래와 같이 설정을 추가
```js
const nextConfig = {
  experimental: {
    after: true,
  },
};
export default nextConfig;

```

- **함수 호출**: `Server Components`, `Server Actions`, `Route Handlers`, 또는 `Middleware`에서 `unstable_after`를 호출할 수 있다.
	- 이 코드에서 `after` 함수 안의 `log()`는 **응답이 사용자에게 완료된 후**에 실행된다. 즉, `return <>{children}</>;`을 통해 화면에 `children`이 렌더링되고, 그 후 `log()` 함수가 비동기적으로 실행된다.
	- **보조 작업 예약**: `after(() => { log(); });`에서 정의된 `log()` 함수는 응답이 끝난 후에 실행될 보조 작업으로 예약된다. 즉, `children`이 렌더링된 후 사용자가 화면을 볼 수 있게 된 상태에서 `log()`가 실행되는 것이다.
```js
import { unstable_after as after } from 'next/server';
import { log } from '@/app/utils';

export default function Layout({ children }) {
  // 보조 작업
  after(() => {
    log();
  });

  // 주 작업
  return <>{children}</>;
}
```

> **서버리스(Serverless)란?**
- 서버리스(Serverless)는 개발자가 서버를 직접 관리하거나 운영할 필요 없이 애플리케이션을 구축하고 실행할 수 있는 클라우드 컴퓨팅 모델을 의미한다. 이 모델에서 "**서버리스(Serverless)**" 라는 용어는 실제로 서버가 없다는 뜻이 아니라, 서버의 관리와 운영을 클라우드 서비스 제공자가 담당한다는 것을 의미한다.

- 이러한 방식은 개발자가 코드에 집중할 수 있게 하고, 서버 관리의 복잡성을 줄여준다. 서버리스 아키텍처의 주요 요소는 다음과 같다.
	1. **이벤트 기반 실행**: 서버리스 함수는 특정 이벤트(예: HTTP 요청, 파일 업로드, 데이터베이스 변경 등)가 발생할 때 자동으로 실행된다.
	2. **스케일링**: 서버리스 플랫폼은 수요에 따라 자동으로 스케일 업(또는 스케일 다운)한다. 즉, 트래픽이 증가하면 더 많은 인스턴스가 실행되고, 트래픽이 감소하면 인스턴스 수가 줄어든다.
	3. **비용 효율성**: 사용한 만큼만 요금을 지불한다. 서버가 항상 실행되고 있는 것이 아니라, 함수가 호출될 때만 실행되므로 비용을 절감할 수 있다.
	4. **관리의 용이성**: 서버를 직접 관리할 필요가 없기 때문에, 운영 시스템, 보안 패치 및 유지보수와 같은 작업에서 벗어날 수 있다.
    
- 예시 플랫폼은 아래와 같다.
	1. **AWS Lambda**: AWS의 서버리스 컴퓨팅 서비스로, 다양한 AWS 서비스와 통합되어 이벤트 기반으로 코드를 실행할 수 있다.
	2. **Azure Functions**: Microsoft Azure의 서버리스 서비스로, HTTP 요청, 타이머, 메시지 큐 등 다양한 이벤트에 반응할 수 있다.
	3. **Google Cloud Functions**: Google Cloud에서 제공하는 서버리스 함수로, HTTP 요청이나 Google Cloud Storage의 이벤트 등으로 트리거된다.
	4. **Vercel Functions / Netlify Functions**: 프론트엔드 애플리케이션과 통합된 서버리스 함수를 쉽게 배포할 수 있는 플랫폼


### 13. `instrumentation.js` (Stable)

- `instrumentation.js` 파일에서 `register()` API를 사용하면 **Next.js 서버의 생명주기(라이프사이클)를 모니터링**할 수 있다. 이를 통해 성능을 모니터링하고 오류의 근원을 추적할 수 있으며, OpenTelemetry와 같은 관찰 가능성(Observability) 라이브러리와의 통합도 지원한다.

- **`onRequestError` Hook**: Sentry와 협력하여 만든 이 새로운 `onRequestError` hook을 사용하면 서버에서 발생하는 모든 오류에 대해 다음을 수행할 수 있다:
    - 오류가 발생한 router와 Server Context(페이지 라우터, 서버 컴포넌트, 서버 액션, 라우트 핸들러 등)에 대한 중요한 정보를 캡처한다.
    - 오류를 선호하는 관찰 가능성 제공자(예: Sentry)에 보고할 수 있다.

```js
export async function onRequestError(err, request, context) {
  await fetch('https://...', {
    method: 'POST',
    body: JSON.stringify({ message: err.message, request, context }),
    headers: { 'Content-Type': 'application/json' },
  });
}

```

- 쉽게 말해 `onRequestError` 함수는 서버에서 발생한 오류를 추적할 수 있도록 하는 선택적 함수로, 이 함수를 사용하면 서버 오류를 원하는 관측 도구(예: Sentry)에 기록할 수 있다.
	- **비동기 작업 처리**: `onRequestError`에서 비동기 작업(예: 오류 정보를 외부 서버에 전송)을 실행하는 경우, 반드시 `await`를 사용하여 해당 작업이 완료될 때까지 기다려야 한다. 이는 오류가 발생했을 때 `onRequestError`가 비동기 작업을 완료할 시간을 확보하기 위해 필요하다.
    
	- **오류 인스턴스**: 오류가 발생한 후, `onRequestError`에 전달되는 오류 인스턴스는 원본 오류와 다를 수 있다. 특히 서버 컴포넌트 렌더링 중 오류가 발생한 경우, React가 오류를 내부적으로 처리할 수 있기 때문이다. 이 경우, `digest` 속성을 통해 오류의 실제 유형을 식별할 수 있다. `digest`는 원래 발생한 오류의 특성을 파악하는 데 유용한 정보이다.


### 14. `<Form>` Component

- `<Form>` 컴포넌트는 **HTML `<form>` 요소를 확장**하여 사전 로딩(prefetching), 클라이언트 사이드 네비게이션, 점진적 향상(Progressive Enhancement)을 제공한다. 이 컴포넌트는 검색 결과 페이지와 같이 페이지를 이동하는 폼에 특히 유용하다.
	- **사전 로딩**: 폼이 화면에 표시되면 해당 레이아웃과 로딩 UI를 미리 로드하여 빠른 네비게이션을 제공한다.
	- **클라이언트 사이드 네비게이션**: 폼 제출 시 레이아웃과 클라이언트 사이드 상태가 유지된다.
	- **점진적 향상**: JavaScript가 로드되지 않은 상태에서도 전체 페이지 네비게이션을 통해 폼이 정상 작동한다.
```js
import Form from 'next/form';

export default function Page() {
  return (
    <Form action="/search">
      <input name="query" />
      <button type="submit">Submit</button>
    </Form>
  );
}
```


### 15. Support for `next.config.ts` (Typescript 지원을 위한 `next.config.ts`)

- Next.js는 이제 TypeScript 기반의 `next.config.ts` 파일을 지원하며, **`NextConfig` 타입**을 제공하여 자동 완성 및 타입 안전한 옵션 구성을 돕는다.
```ts
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  /* 구성 옵션 */
};

export default nextConfig;
```


### 16. Improvements for self-hosting

1. **캐시 제어 개선**:  
	- `self-hosting` 시, `Cache-Control` 지시문을 보다 세밀하게 제어할 수 있도록 `expireTime` 설정이 가능해졌다.
	- 이 설정을 통해 ISR 페이지의 `stale-while-revalidate` 기간을 쉽게 조정할 수 있다.
	- 기본값은 1년으로 설정되었고, 대부분의 CDN이 이를 통해 최적의 캐시 재검증 전략을 활용할 수 있게 된다.

2. **이미지 최적화**:  
	- `self-hosting` 시 이미지 최적화를 위해 `sharp`을 수동 설치하지 않아도 된다.
	- `Next.js v15` 에서는 서버 실행 시 자동으로 `sharp`을 활용하여 이미지 최적화를 지원한다.

- 참고자료 : https://x.com/leeerob/status/1843796169173995544


### 17. Enhanced Security for Server Actions (Server Actions 보안강화)

- 서버에서 호출할 수 있는 **Server Actions**는 사용되지 않는 코드에 대한 제거, 안전한 ID 생성과 같은 보안 기능이 추가되었다.
	- `Server Action`은 여전히 HTTP로 접근 가능한 엔드포인트로 취급되므로 보안 관리를 주의해야 한다.

- **불필요한 코드 제거**: 사용되지 않는 `Server Action`은 빌드 시 제거된다.
- **안전한 ID 생성**: 클라이언트가 액션을 참조할 수 있도록 `Next.js`가 안전하고 예측 불가능한 ID를 생성한다.

```js
// app/actions.js
'use server';

// 이 action은 애플리케이션에서 사용되고 있기 때문에, Next.js는 클라이언트가 이 Server Action을 참조하고 호출할 수 있도록
// 보안 ID를 생성합니다.
export async function updateUserAction(formData) {}

// 이 action은 애플리케이션에서 사용되지 않기 때문에, Next.js는 `next build` 중 이 코드를 자동으로 제거하며
// 공개 엔드포인트를 생성하지 않습니다.
export async function deleteUserAction(formData) {}
됨
```


### 18. Optimizing bundling of external packages (Stable)

- **외부 패키지 번들링**은 앱의 cold start 성능을 향상시킨다.
	- 이제, `Pages Router`와 `App Router`에서 번들링을 세부적으로 설정할 수 있게 되었다. 
	- 예를 들어, `serverExternalPackages`를 사용해 특정 패키지를 번들링에서 제외할 수 있다.

```js
const nextConfig = {
  bundlePagesRouterDependencies: true,
  serverExternalPackages: ['package-name'],
};
export default nextConfig;
```


> **Cold Start  
- Cold Start 방식은 컴퓨터 과학과 클라우드 컴퓨팅에서 사용되는 용어로, 서버가 처음 요청을 처리할 때 초기화하는 데 걸리는 시간과 리소스를 의미한다.

- 예를 들어, 인터넷에 연결된 서비스가 처음 켜질 때, 모든 시스템이 준비되고 정상적으로 작동하기까지는 시간이 걸리는데, 이 때 시스템이 느리게 작동하거나 잠시 반응이 없을 수 있다. 
	- 서버리스 함수는 일정 시간 사용되지 않으면 중지되고, 그다음 요청이 들어오면 처음부터 다시 시작되는데. 이때 함수 초기화, 의존성 로드, 데이터베이스 연결 설정 등 시간이 필요하게 된다. 이 초기화 과정이 “Cold Start”이다.
	- Cold Start는 지연 시간을 증가시켜 사용자 응답 시간이 길어질 수 있다. 특히 클라우드 서비스 같은 경우, 이 Cold Start 시간을 줄이는 것이 중요한데, 서비스가 빨리 시작되고 잘 작동하는 것이 사용자 경험과 비용 관리에 큰 영향을 미치기 때문이다.
  
- 반면 **Warm Start**는 이전 요청을 처리한 후 바로 이어지는 요청으로, 초기화 과정이 생략되어 빠르게 처리되는 경우를 말한다.


### 19. ESLint 9 Support

- Next.js 15에서는 ESLint 8의 지원 종료일인 2024년 10월 5일 이후 ESLint 9에 대한 지원을 도입했다.

- Next.js는 이전 버전과의 호환성을 유지하여, ESLint 8과 9 중 어느 버전이든 계속 사용할 수 있다. 
	- ESLint 9로 업그레이드했을 때 새 설정 형식을 아직 사용하지 않는 경우, Next.js가 자동으로 `ESLINT_USE_FLAT_CONFIG=false` 설정을 적용하여 전환을 돕는다.

- 또한, `—ext`와 `—ignore-path`와 같은 더 이상 사용되지 않는 옵션들은 `next lint` 실행 시 제거된다. 이러한 옵션들은 ESLint 10에서 최종적으로 지원되지 않으므로, 빠른 [마이그레이션](https://eslint.org/docs/latest/use/migrate-to-9.0.0)(데이터나 소프트웨어를 한 시스템에서 다른 시스템으로 이동하는 것)을 권장한다.

- 이번 업데이트의 일환으로, React Hooks 사용을 위한 새 규칙이 포함된 `eslint-plugin-react-hooks` 버전 5.0.0으로도 업그레이드되었습니다. 모든 변경 사항은 [`eslint-plugin-react-hooks@5.0.0`](https://github.com/facebook/react/releases/tag/eslint-plugin-react-hooks%405.0.0)의 변경 로그에서 확인할 수 있습니다.


### 20. Development and Build Improvements

1. **Server Components HMR**:  
	- 개발 중에, `Hot Module Replacement (HMR)` 기능은 fetch 요청의 응답을 재사용하여 개발 성능을 향상시키고 API 호출 비용을 줄인다.

2. **정적 생성 최적화**:  
	- 앱의 정적 페이지 생성 시, 기존에는 두 번 렌더링을 거쳤지만, 이제 첫 번째 렌더링 데이터를 재사용해 빌드 시간을 줄였다.

3. **정적 생성 고급 제어**:  
	- 페이지 생성 실패 시 재시도 횟수와 같은 정적 생성의 고급 설정을 지원한다. 기본 설정을 권장하지만 특정 상황에서 사용자 정의할 수도 있다.

```ts
const nextConfig = {
  experimental: {
	// Next.js가 페이지 생성에 실패했을 때, 빌드가 실패하기 전에 몇 번 재시도할지 설정합니다.
	staticGenerationRetryCount: 1
	
	// 한 워커(worker)당 처리할 페이지 수를 설정합니다.
	staticGenerationMaxConcurrency: 8
	
	// 새로운 export 워커를 생성하기 위한 최소 페이지 수를 설정합니다.
	staticGenerationMinPagesPerWorker: 25
  },
};
export default nextConfig;
```