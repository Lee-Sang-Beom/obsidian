
#### 1. Turbopack for Development (Release Candidate)

- Next.js v14.2부터 Turbopack Release Candidate가 로컬 개발용으로 사용 가능하다고 한다.
	- 로컬 서버 시작 속도 최대 76.7% 향상
	- Fast Refresh를 사용한 코드 업데이트 속도 최대 96.3% 향상
	- 디스크 캐싱 없이 초기 라우트 컴파일 속도 최대 45.8% 향상 (Turbopack은 아직 디스크 캐싱이 없다.)

#### 2. Tree-shaking

- Tree-shaking(트리 쉐이킹)이란, 자바스크립트 모듈 번들링 도구가 번들링 시에 사용하는 번들링 기술 중 하나이다.
	- 이 기술은 빌드(production)에서 사용되며, 사용하지 않는 코드를 자동으로 제거하여 번들 크기를 최소화한다.

- 본 업데이트에서는, 서버 및 클라이언트 컴포넌트 간 boundary 최적화를 식별했다고 한다.
	- 긴 내용을 정리하면, 사용되지 않는 export를 제거하여 트리 쉐이킹을 가능하게 했다는 것이 결론이다.

> [!note] `optimizePackageImports`
> - 이 최적화 과정은 현재 barral 파일들에게서는 작동하지 않는다.
> - 그동안 `next.config.ts`파일에서, `optimizePackageImports` 구성 옵션을 사용할 수 있다.
```ts
// next.config.ts
module.exports = { experimental: { optimizePackageImports: ['package-name'], },};
```


#### 3. Build Memory Usage

- 대규모 Next.js 애플리케이션의 경우, 프로덕션 빌드 중 메모리 부족(OOM) 문제가 발생했다고 한다.
	- 사용자 보고서와 재현을 조사한 결과, 해당 이슈는 **과도한 번들링** 과 **최소화**가 관련되어 있다고 한다.
		- 최소화: Next.js가 더 적은 수의 큰 JavaScript 파일을 중복 생성하는 것
	
	- 그래서, 이번 업데이트에서 번들링 로직을 재구성하고 이러한 경우를 위해 컴파일러를 최적화하였다.

- 많은 의존성을 가진 애플리케이션은 더 많은 메모리를 사용한다. 
	- 번들 분석기를 사용하면, 애플리케이션에서 큰 비중을 가진 의존성을 조사하여 성능과 메모리 사용량을 향상시키기 위해 제거할 수 있는 항목을 찾을 수 있다.

- `--experimental-debug-memory-usage` 옵션을 사용하여 `next build --experimental-debug-memory-usage`를 실행하여 메모리 사용량에 대한 정보를 지속적으로 출력하는 모드에서 빌드를 실행할 수 있다.
	- 이 모드에서는 Next.js가 Heap 사용량 및 가비지 수집 통계와 같은 정보를 지속적으로 출력한다.
	- 또한 메모리 사용량이 구성된 한계에 근접할 경우, 자동으로 Heap Snapshot이 촬영된다.


#### 4. CSS

-  페이지 간 이동 시 충돌하는 스타일을 피하기 위해 Next.js 빌드(production) 중 CSS를 **청크로 분할하는 방식**이 업데이트되었다.

- 이제 CSS 청크의 순서와 병합은 import 순서에 의해 정의된다.
	- 예를 들어, `base-button.module.css`는 `page.module.css` 앞에 정렬될 것이다.

```tsx
// page.tsx
import { BaseButton } from './base-button'; 
import styles from './page.module.css'; 
export function Page() { return <BaseButton className={styles.primary} />; }
```
```tsx
// base-button.tsx
import styles from './base-button.module.css'; 
export function BaseButton() { return <button className={styles.primary} />; }
```

- Next.js v14.2 업데이트에서는, 올바른 CSS 순서를 유지하기 위해 아래 내용을 권장하고 있다.
	- 전역 스타일보다 **CSS module** 사용
	- CSS 모듈은 단일 JS/TS 파일에서만 가져와야 한다.
	- 전역 클래스 이름을 사용하는 경우 전역 스타일을 동일한 `.ts / .js` 파일에 가져와야 한다.

- 이 변경이 대부분의 애플리케이션에 부정적인 영향을 미치지 않을 것으로 예상하지만, 버전 업그레이드 후, 상치 못한 스타일이 발생한다면 위에서 권장하는 대로 CSS import 순서를 검토해야 한다.

- 예를 들어, 위의 코드는 `page.tsx`와` base-button.tsx`에서 CSS 모듈을 가져오는 변수명이 `styles`로  동일하다.
	- 때문에, `page.module.css` 내용이 `base-button.module.css`의 내용을 덮어쓰게 된다.


#### 5. Caching Improvements -> `staleTimes`

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


#### 7. Parallel and Intercepting Routes

- Next.js 측에서는 계속해서 "**Parallel and Intercepting Routes**" 기능을 개선하고 있으며, 이제 클라이언트 측 라우터 캐시와의 통합을 향상시키고 있다고 설명하고 있다.

-  "**Parallel and Intercepting Routes**"란, **Server Action**을 호출할 때 `revalidatePath` 또는 `revalidateTag`를 사용하는 route를 의미하며, 이러한 route는 캐시를 재검증하고 사용자의 현재 뷰를 유지하면서 보이는 슬롯을 새로 고친다.
	- 마찬가지로, `router.refresh()`를 호출하면 현재 보이는 슬롯을 정확하게 새로 고칠 수 있으며, 동시에 현재 뷰를 유지할 수도 있다.


#### 8. Errors DX Improvements

- Next.js v14.1 버전에서는 오류 메시지 및 stack trace에 대한 가독성이 향상되었다. 
	- 그리고 v14.2 버전에서는 개선된 오류 메시지, App Router 및 Pages Router에 대한 개선된 오버레이 디자인, 라이트 모드와 다크 모드 지원, 그리고 개발 및 빌드 과정에서 더 명확한 로그를 포함하도록 확장되었다.

> [이미지 출처: Next.js 14.2 업데이트문서](https://nextjs.org/blog/next-14-2)

![[오류메시지 전.png]]
![[오류메시지 후.png]]

- 예를 들어, **React Hydration**오류가 있다.
	-  React Hydration 불일치의 원인을 정확하게 파악하는 데 도움을 주기 위해, 아래 이미지와 같은 노력을 가했다고 한다.
	- React 팀과 협력하여 핵심 오류 메시지를 개선하고 오류가 발생한 파일 이름을 표시하도록 노력하고 있다고 한다. 
		- 개인적으로 어느 위치에 대한 에러 메시지인지 이제 한 눈에 확인이 가능해져서 **완전 편하다고 생각함!**


#### 9. React 19

- 2월에, React 팀은 React 19가 곧 출시될 일정이라고 발표했다.
	- React 19에 대비하여, 최신 기능과 개선 사항을 Next.js에 통합하는 작업을 진행 중이며, 이러한 변경 사항을 조율하기 위해 주요 버전을 출시할 계획이라고 한다.

- 이제 [React 캐너리 채널](https://react.dev/blog/2023/05/03/react-canaries)에서, Next.js 내에서 사용할 수 있는 작업 및 관련 hook와 같은 새로운 기능을 이제 모든 React 애플리케이션(클라이언트 전용 애플리케이션 포함)에서 사용할 수 있다고 한다.
