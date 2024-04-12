
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

- 예를 들어, 위의 코드는 `page.tsx`와` base-button.tsx`에서 CSS 모듈을 가져오는 변수명이 `styles`로  동일한 하다.
	- 때문에, `page.module.css` 내용이 `base-button.module.css`의 내용을 덮어쓰게 됩니다.

이는 CSS 모듈이 각각의 컴포넌트 범위에 지역적으로 작용하기 때문에 발생합니다. 따라서 두 개 이상의 모듈에서 동일한 클래스명을 사용하더라도 각 모듈이 서로 영향을 주지 않고 독립적으로 동작합니다.

그러나 이러한 작동 방식 때문에 동일한 클래스명을 사용할 때 주의가 필요합니다. 만약 두 모듈에서 같은 클래스명을 사용하는데 의도치 않게 스타일이 충돌한다면, 해당 클래스명을 고유하게 만들어야 합니다.