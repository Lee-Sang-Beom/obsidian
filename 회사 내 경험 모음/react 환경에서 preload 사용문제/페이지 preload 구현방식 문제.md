
## 1. Preload 사용 시 발생하는 문제점

### 1.1 브라우저 경고 발생

```
The resource https://podcast.82alda.co.kr/src/pages/signal/_components/signal-part/chart-toggle.tsx was preloaded using link preload but not used within a few seconds from the window's load event.
```

### 1.2 문제 원인

- **리소스 미사용**: `preload`로 요청한 리소스가 실제로는 사용되지 않음
- **성능 저하**: 불필요한 네트워크 요청으로 인한 대역폭 낭비
- **브라우저 최적화 방해**: 브라우저의 리소스 우선순위 결정 알고리즘에 혼란

## 2. React/Vite 환경에서의 구체적 문제점

### 2.1 경로 불일치 문제

```typescript
// 문제가 되는 코드
preload(route.path, { as: 'script' })
// route.path = '@/pages/signal/signal-page.tsx' (원본 소스 경로)
```

**원인 분석:**

- `import.meta.glob`으로 가져온 경로는 **개발 시점의 원본 소스 경로**
- Vite 빌드 시 실제 생성되는 JavaScript 번들 파일 경로와 **불일치**
- 브라우저는 존재하지 않는 `.tsx` 파일을 preload하려고 시도

### 2.2 Vite 번들링 시스템과의 충돌

- **개발 환경**: ESM 모듈 시스템으로 동적 변환
- **프로덕션 환경**: 청크 단위로 번들링되어 해시가 포함된 파일명으로 변경
- **결과**: `preload`가 참조하는 경로와 실제 번들 파일 경로 불일치

### 2.3 실제 동작 과정에서의 문제

1. `preload(route.path, { as: 'script' })` 실행
2. 브라우저가 `@/pages/signal/signal-page.tsx` 파일을 찾으려 시도
3. **404 에러 또는 파일을 찾을 수 없음**
4. 동시에 `route.importFn()` 실행으로 실제 모듈은 정상 로드
5. 브라우저가 preload된 리소스가 사용되지 않았다고 판단하여 경고 발생

## 3. Preload 미사용 방식의 이점

### 3.1 기술적 이점

- **경고 제거**: 브라우저 콘솔의 preload 관련 경고 완전 해결
- **정확한 리소스 로딩**: Vite의 모듈 시스템과 완벽 호환
- **코드 단순화**: preload 관련 복잡한 로직 제거

### 3.2 성능 이점

- **불필요한 네트워크 요청 제거**: 실패하는 preload 요청 방지
- **메모리 효율성**: preload 실패로 인한 메모리 누수 방지
- **정확한 캐싱**: 동적 import만으로도 충분한 모듈 캐싱 효과

### 3.3 유지보수 이점

- **단순한 구조**: preload 실패 처리 로직 불필요
- **안정성 향상**: 브라우저 호환성 문제 해결
- **디버깅 용이**: 불필요한 경고로 인한 디버깅 방해 요소 제거

## 4. 최종 권장사항

### 4.1 현재 구현 방식 (권장)

```typescript
// 단순하고 효과적인 동적 import만 사용
const prefetchPromise = route.importFn()
  .then(module => {
    console.log(`Successfully prefetched: ${route.name}`)
    return module
  })
```

### 4.2 성능 검증 결과

- **페이지 로딩 속도**: 기존과 동일한 성능 유지
- **모듈 캐싱**: `prefetchCache`를 통한 효과적인 캐시 활용
- **사용자 경험**: preload 경고 제거로 개발자 도구 정리

### 4.3 결론

**Vite + React 환경에서는 `preload` 없이 순수 동적 import만으로도 충분한 프리페치 성능을 달성할 수 있으며, 오히려 더 안정적이고 유지보수하기 쉬운 구조를 제공합니다.**