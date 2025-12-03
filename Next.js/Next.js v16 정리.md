
> 📅 릴리즈: 2025년 10월 21일  
> 🔗 공식 문서: [Next.js 16 Release](https://nextjs.org/blog/next-16)

## 🎯 개요

Next.js 16은 성능, 캐싱 전략, 그리고 React 최신 기능과의 통합에 초점을 맞춘 메이저 업데이트입니다. 이번 릴리즈의 핵심은 **명시적이고 예측 가능한 캐싱**, **빌드 성능 향상**, 그리고 **개발자 경험 개선**입니다.

---

## ✨ 주요 신기능

### 1. Cache Components - 캐싱의 새로운 패러다임

#### 개념

기존 App Router의 암묵적(implicit) 캐싱 방식에서 벗어나, **명시적(explicit) 캐싱**으로 전환되었습니다.

Cache Components는 `use cache` 디렉티브를 중심으로 하며, 페이지, 컴포넌트, 함수를 캐시 가능하게 만들 수 있습니다.

#### 주요 특징

- **기본 동작 변경**: 모든 동적 코드는 기본적으로 요청 시점에 실행되며, 캐싱은 완전히 opt-in 방식
- **Partial Pre-Rendering (PPR) 완성**: 정적 콘텐츠와 동적 콘텐츠를 하나의 페이지에서 조합 가능
- **자동 캐시 키 생성**: 컴파일러가 자동으로 캐시 키를 생성

#### 설정 방법

```typescript
// next.config.ts
const nextConfig = {
  cacheComponents: true,
};
export default nextConfig;
```

#### 사용 예시

**1. 페이지 레벨 캐싱**

```typescript
// app/blog/page.tsx
"use cache";

export default async function BlogPage() {
  const posts = await fetchPosts();
  return (
    <div>
      {posts.map(post => (
        <Article key={post.id} {...post} />
      ))}
    </div>
  );
}
```

**2. 컴포넌트 레벨 캐싱**

```typescript
async function ProductList() {
  "use cache";
  const products = await getProducts();
  return (
    <>
      {products.map(product => (
        <p key={product.id}>{product.name}</p>
      ))}
    </>
  );
}
```

**3. 함수 레벨 캐싱**

```typescript
async function getProducts() {
  "use cache";
  cacheTag("products");
  const products = await db.query("SELECT * FROM products");
  return products;
}
```

#### 관련 설정 제거

- `experimental.ppr` 플래그 제거
- `experimental.dynamicIO` 플래그 제거
- `export const dynamic = 'force-dynamic'` 불필요

---

### 2. Turbopack - 안정화 및 기본 번들러 지정

#### 성능 개선

프로덕션 빌드가 2-5배 빠르고, Fast Refresh가 최대 10배 향상되었습니다.

#### 주요 변경사항

- **안정화**: 베타에서 안정 버전으로 승격
- **기본 번들러**: 모든 새 Next.js 프로젝트의 기본 번들러
- **광범위한 채택**: Next.js 15.3+ 이상에서 개발 세션의 50%, 프로덕션 빌드의 20% 이상이 Turbopack 사용

#### Webpack 사용 방법

여전히 webpack이 필요한 경우:

```bash
next dev --webpack
next build --webpack
```

#### 파일시스템 캐싱 (베타)

대규모 프로젝트를 위한 추가 성능 향상 기능:

```typescript
// next.config.ts
const nextConfig = {
  experimental: {
    turbopackFileSystemCacheForDev: true,
  },
};
export default nextConfig;
```

**효과**: 재시작 간 컴파일 아티팩트를 디스크에 저장하여 컴파일 시간 대폭 단축

---

### 3. 개선된 캐싱 API

#### `revalidateTag()` - 업데이트됨

이제 stale-while-revalidate (SWR) 동작을 위한 cacheLife 프로필이 두 번째 인자로 필수입니다.

```typescript
import { revalidateTag } from 'next/cache';

// ✅ 권장: 대부분의 경우 'max' 사용
revalidateTag('blog-posts', 'max');

// 다른 내장 프로필 사용
revalidateTag('news-feed', 'hours');
revalidateTag('analytics', 'days');

// 커스텀 재검증 시간 사용
revalidateTag('products', { expire: 3600 });

// ⚠️ 더 이상 사용 불가
revalidateTag('blog-posts'); // 에러!
```

**사용 시나리오**:

- 최종 일관성을 허용할 수 있는 콘텐츠
- 사용자가 캐시된 데이터를 즉시 받고 백그라운드에서 재검증

#### `updateTag()` - 신규

Server Action 전용 API로 read-your-writes 시맨틱을 제공합니다.

```typescript
'use server';
import { updateTag } from 'next/cache';

export async function updateUserProfile(userId: string, profile: Profile) {
  await db.users.update(userId, profile);
  
  // 캐시 만료 및 즉시 새로운 데이터 읽기
  updateTag(`user-${userId}`);
}
```

**사용 시나리오**:

- 폼 제출
- 사용자 설정 변경
- 사용자가 즉시 변경사항을 봐야 하는 모든 경우

#### `refresh()` - 신규

캐시되지 않은 데이터만 새로고침하는 Server Action 전용 API입니다.

```typescript
'use server';
import { refresh } from 'next/cache';

export async function markNotificationAsRead(notificationId: string) {
  await db.notifications.markAsRead(notificationId);
  
  // 헤더의 알림 카운트만 새로고침 (캐시되지 않은 데이터)
  refresh();
}
```

**사용 시나리오**:

- 알림 카운트
- 라이브 메트릭
- 상태 표시기
- 캐시되지 않은 동적 데이터

---

### 4. proxy.ts (이전 middleware.ts)

#### 변경 사항

middleware.ts가 proxy.ts로 이름 변경되어 앱의 네트워크 경계를 명확히 함

**마이그레이션 방법**:

```bash
# 파일 이름 변경
mv middleware.ts proxy.ts
```

```typescript
// proxy.ts
import { NextRequest, NextResponse } from 'next/server';

export default function proxy(request: NextRequest) {
  return NextResponse.redirect(new URL('/home', request.url));
}
```

#### 주요 변경점

|변경 전|변경 후|
|---|---|
|`middleware.ts`|`proxy.ts`|
|`export function middleware()`|`export default function proxy()`|
|`skipMiddlewareUrlNormalize`|`skipProxyUrlNormalize`|

⚠️ **주의**:

- proxy.ts는 Node.js 런타임에서만 실행
- Edge 런타임이 필요한 경우 middleware.ts를 계속 사용 (deprecated)

---

### 5. Next.js DevTools MCP

Model Context Protocol을 통한 AI 기반 디버깅 도구입니다.

#### 기능

AI 에이전트에 Next.js 지식, 통합 로그, 자동 에러 접근, 페이지 인식 기능을 제공합니다.

- **라우팅, 캐싱, 렌더링 동작 이해**
- **브라우저와 서버 로그 통합**: 컨텍스트 전환 없이 확인
- **자동 에러 접근**: 수동 복사 없이 상세한 스택 트레이스 제공
- **페이지 인식**: 활성 라우트에 대한 컨텍스트 이해

#### 활용

AI 도구가 프로젝트 구조를 이해하여:

- 이슈 진단
- 동작 설명
- 개발 워크플로우 내에서 직접 수정 제안

---

### 6. React 19.2 통합

Next.js 16은 React 19.2의 최신 기능들을 포함한 React Canary 릴리스를 사용합니다.

#### 새로운 기능

**View Transitions**

```typescript
// Transition 내부에서 애니메이션 적용
<Transition>
  <ElementThatAnimates />
</Transition>
```

**useEffectEvent()**

```typescript
// Effect에서 비반응형 로직을 재사용 가능한 함수로 추출
const onVisit = useEffectEvent(() => {
  logVisit(url);
});
```

**Activity 컴포넌트**

```typescript
// display: none으로 UI를 숨기면서 상태 유지 및 Effect 정리
<Activity>
  <BackgroundTask />
</Activity>
```

---

### 7. 향상된 라우팅 및 네비게이션

#### Layout Deduplication

여러 URL이 공유 레이아웃을 가질 때, 레이아웃을 한 번만 다운로드합니다.

**예시**: 50개의 제품 링크가 있는 페이지에서 공유 레이아웃을 50번이 아닌 1번만 다운로드

#### Incremental Prefetching

Next.js는 캐시에 없는 부분만 프리페치하며, 전체 페이지를 프리페치하지 않음

**특징**:

- 링크가 뷰포트를 벗어나면 요청 취소
- 호버 시 또는 뷰포트에 재진입 시 우선순위 지정
- 데이터가 무효화되면 링크 재프리페치
- Cache Components와 원활하게 작동

**트레이드오프**:

- ✅ 개별 프리페치 요청이 더 많을 수 있음
- ✅ 하지만 총 전송 크기는 훨씬 작음

---

### 8. 간소화된 create-next-app

설정 플로우 단순화, 업데이트된 프로젝트 구조, 개선된 기본값

#### 새 템플릿 기본값

- ✅ App Router (기본)
- ✅ TypeScript 우선 설정
- ✅ Tailwind CSS
- ✅ ESLint

```bash
npx create-next-app@latest
```

---

### 9. React Compiler 지원 - 안정화

React Compiler의 1.0 릴리스에 따라 Next.js 16에서 안정화

#### 기능

- **자동 메모이제이션**: 컴포넌트를 자동으로 메모이제이션하여 불필요한 리렌더링 감소
- **수동 코드 변경 불필요**: `useMemo`, `useCallback` 등을 수동으로 추가할 필요 없음

#### 설정

```typescript
// next.config.ts
const nextConfig = {
  reactCompiler: true,
};
export default nextConfig;
```

```bash
npm install babel-plugin-react-compiler@latest
```

⚠️ **주의**:

- 기본적으로 비활성화 (빌드 성능 데이터 수집 중)
- 개발 및 빌드 시 컴파일 시간이 증가할 수 있음 (Babel 의존성)

---

### 10. Build Adapters API (알파)

커스텀 빌드 통합을 위한 새로운 API입니다.

#### 용도

- 배포 플랫폼이 빌드 프로세스에 hook
- Next.js 설정 수정
- 빌드 출력 처리

```javascript
// next.config.js
const nextConfig = {
  experimental: {
    adapterPath: require.resolve('./my-adapter.js'),
  },
};
module.exports = nextConfig;
```

---

### 11. 로깅 개선

#### 개발 요청 로그

시간이 소요되는 위치를 명확히 표시:

- **Compile**: 라우팅 및 컴파일
- **Render**: 코드 실행 및 React 렌더링

#### 빌드 로그

각 빌드 단계와 소요 시간을 표시:

```
▲ Next.js 16 (Turbopack)
✓ Compiled successfully in 615ms
✓ Finished TypeScript in 1114ms
✓ Collecting page data in 208ms
✓ Generating static pages in 239ms
✓ Finalizing page optimization in 5ms
```

---

## 🔴 Breaking Changes

### 1. 버전 요구사항

|항목|최소 버전|
|---|---|
|Node.js|20.9.0+ (LTS)|
|TypeScript|5.1.0+|
|Chrome|111+|
|Edge|111+|
|Firefox|111+|
|Safari|16.4+|

### 2. Async Params 및 SearchParams

가장 큰 변경사항 중 하나입니다.

#### 변경 전 (Next.js 15)

```typescript
export default function Page({ 
  params,
  searchParams 
}: { 
  params: { id: string };
  searchParams: { sort: string };
}) {
  const id = params.id;  // 직접 접근
  const sort = searchParams.sort;
  return <div>Product {id}</div>;
}
```

#### 변경 후 (Next.js 16)

```typescript
export default async function Page({ 
  params,
  searchParams 
}: { 
  params: Promise<{ id: string }>;
  searchParams: Promise<{ sort: string }>;
}) {
  const { id } = await params;  // await 필수
  const { sort } = await searchParams;
  return <div>Product {id}</div>;
}
```

### 3. 동적 함수들도 Async

```typescript
// 변경 전
import { cookies } from 'next/headers';

export function getAuthToken() {
  const cookieStore = cookies();
  return cookieStore.get('token');
}

// 변경 후
import { cookies } from 'next/headers';

export async function getAuthToken() {
  const cookieStore = await cookies();
  return cookieStore.get('token');
}
```

**적용 대상**:

- `cookies()`
- `headers()`
- `draftMode()`

### 4. 제거된 기능

|제거된 기능|대체 방안|
|---|---|
|AMP 지원|제거됨 (Google도 더 이상 우선순위 두지 않음)|
|`next lint` 명령어|Biome 또는 ESLint를 직접 사용|
|`serverRuntimeConfig`, `publicRuntimeConfig`|환경 변수 (.env 파일) 사용|
|`experimental.turbopack` 위치|최상위 `turbopack`으로 이동|
|`experimental.dynamicIO`|`cacheComponents`로 이름 변경|
|`experimental.ppr`|Cache Components로 진화|
|`unstable_rootParams()`|향후 마이너 릴리스에서 대체 API 제공 예정|

### 5. 동작 변경

#### 기본 번들러

- **변경**: Turbopack이 모든 앱의 기본 번들러
- **옵트아웃**: `next build --webpack`

#### 이미지 관련

- **`images.minimumCacheTTL`**: 60초 → 4시간 (14400초)
- **`images.imageSizes`**: 기본값에서 `16` 제거 (4.2%만 사용)
- **`images.qualities`**: `[1..100]` → `[75]`
- **`images.maximumRedirects`**: 무제한 → 3회

#### next/image 로컬 소스

쿼리 문자열이 있는 로컬 이미지 소스는 열거 공격 방지를 위해 images.localPatterns.search 설정이 필요

#### Parallel Routes

모든 parallel route 슬롯에 명시적인 `default.js` 파일 필요

---

## ⚠️ Deprecated (향후 제거 예정)

|기능|대체 방안|
|---|---|
|`middleware.ts` 파일명|`proxy.ts`로 변경|
|`next/legacy/image`|`next/image` 사용|
|`images.domains`|`images.remotePatterns` 사용|
|`revalidateTag()` 단일 인자|`revalidateTag(tag, profile)` 또는 `updateTag(tag)`|

---

## 🚀 마이그레이션 가이드

### 자동 마이그레이션

Next.js는 자동 codemod를 제공합니다:

```bash
npx @next/codemod@canary upgrade latest
```

### 수동 업그레이드

```bash
npm install next@latest react@latest react-dom@latest
```

### 새 프로젝트 시작

```bash
npx create-next-app@latest
```

---

## 📊 성능 개선 요약

|항목|개선 정도|
|---|---|
|프로덕션 빌드|2-5배 빠름|
|Fast Refresh|최대 10배 빠름|
|파일시스템 캐싱|대규모 앱에서 시작 시간 대폭 단축|
|Layout Deduplication|네트워크 전송 크기 대폭 감소|
|Incremental Prefetching|총 전송 크기 감소|

---

## 🎯 주요 사용 사례별 가이드

### Case 1: 블로그 사이트

```typescript
// 전체 페이지 캐싱
"use cache";

export default async function BlogPage() {
  const posts = await fetchPosts();
  return <PostList posts={posts} />;
}
```

### Case 2: 전자상거래

```typescript
// 제품 목록은 캐싱, 사용자 장바구니는 동적
export default async function ProductPage() {
  return (
    <>
      <ProductList />  {/* use cache */}
      <Suspense fallback={<Loading />}>
        <UserCart />   {/* 동적 */}
      </Suspense>
    </>
  );
}
```

### Case 3: 대시보드

```typescript
// 실시간 데이터는 캐시하지 않음
export default async function Dashboard() {
  const realtimeData = await fetchRealtimeData();
  return <MetricsDashboard data={realtimeData} />;
}
```

---

## 🔗 유용한 링크

- [공식 릴리스 노트](https://nextjs.org/blog/next-16)
- [업그레이드 가이드](https://nextjs.org/docs/app/guides/upgrading/version-16)
- [Cache Components 문서](https://nextjs.org/docs/app/getting-started/cache-components)
- [Turbopack 문서](https://nextjs.org/docs/architecture/turbopack)
- [React 19.2 발표](https://react.dev/blog/2025/10/01/react-19-2)

---

## 💡 마이그레이션 체크리스트

- [ ] Node.js 20.9+ 설치
- [ ] TypeScript 5.1+ 업그레이드
- [ ] `params` 및 `searchParams`를 async로 변경
- [ ] `cookies()`, `headers()`, `draftMode()`에 await 추가
- [ ] `middleware.ts` → `proxy.ts` 변경
- [ ] `revalidateTag()` 호출에 두 번째 인자 추가
- [ ] 제거된 기능 사용 여부 확인
- [ ] `cacheComponents` 플래그 고려
- [ ] 자동 codemod 실행
- [ ] 테스트 및 배포

---

## 🎉 결론

Next.js 16은 성능과 개발자 경험을 크게 개선한 릴리스입니다. 특히:

✅ **명시적 캐싱**으로 더 예측 가능한 동작  
✅ **Turbopack 안정화**로 빠른 빌드  
✅ **개선된 API**로 더 나은 제어  
✅ **React 19.2**로 최신 기능 활용

기존 프로젝트는 breaking changes를 주의 깊게 검토하고, 새 프로젝트는 바로 Next.js 16으로 시작하는 것을 권장합니다!