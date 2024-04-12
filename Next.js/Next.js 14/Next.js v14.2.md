
#### 1. [Turbopack for Development (Release Candidate)](https://nextjs.org/blog/next-14-2#turbopack-for-development-release-candidate)

- Next.js v14.2부터 Turbopack Release Candidate가 로컬 개발용으로 사용 가능하다고 한다.
	- 로컬 서버 시작 속도 최대 76.7% 향상
	- Fast Refresh를 사용한 코드 업데이트 속도 최대 96.3% 향상
	- 디스크 캐싱 없이 초기 라우트 컴파일 속도 최대 45.8% 향상 (Turbopack은 아직 디스크 캐싱이 없다.)

#### 2. Tree-shaking

- 서버 및 클라이언트 컴포넌트 간 boundary 최적화를 식별했다고 한다.
	- 긴 내용을 정리하면, 사용되지 않는 export를 제거하여 트리 쉐이킹을 가능하게 했다는 것이 결론이다.

> [!note] `optimizePackageImports`
> - 이 최적화 과정은 현재 barral 파일들에게서는 작동하지 않는다.
> - 그동안 `next.config.ts`파일에서, `optimizePackageImports` 구성 옵션을 사용할 수 있다.
```ts
// next.config.ts
module.exports = { experimental: { optimizePackageImports: ['package-name'], },};
```