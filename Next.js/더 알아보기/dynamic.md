
`dynamic`은 **Next.js**에서 제공하는 함수로, 컴포넌트를 동적으로 로드하고 서버사이드 렌더링(SSR)을 제어할 수 있도록 도와줍니다. 이는 React의 `React.lazy`와 비슷하지만, **Next.js 환경에 특화**되어 있으며 서버사이드 렌더링과 클라이언트 측에서의 동작을 좀 더 세밀하게 제어할 수 있습니다.

---

### 주요 특징 및 사용 이유

1. **동적 컴포넌트 로딩**
    
    - 컴포넌트를 초기 번들에서 제외하여 필요할 때 로드합니다.
    - 페이지 로드 속도를 개선하거나 특정 기능을 사용자 인터랙션 이후에만 로드할 수 있습니다.
2. **서버사이드 렌더링 제어**
    
    - SSR을 비활성화(`ssr: false`)하거나 클라이언트에서만 렌더링하도록 설정할 수 있습니다.
    - 이는 브라우저 전용 라이브러리(예: Chart.js, 특정 DOM API 의존 코드)를 다룰 때 유용합니다.
3. **로딩 상태 관리**
    
    - `loading` 옵션으로 로딩 중 표시할 컴포넌트를 지정할 수 있습니다.


---

### `dynamic` 함수의 구조
```js
const Component = dynamic(() => import('./YourComponent'), {
  ssr: false, // SSR 비활성화
  loading: () => <p>Loading...</p>, // 로딩 중 표시할 컴포넌트
});

```


#### 파라미터

1. **첫 번째 파라미터: 동적으로 로드할 컴포넌트**
    
    - `import()` 함수로 컴포넌트를 지정.
2. **옵션 객체: SSR 및 로딩 상태 설정**
    
    - `ssr`: 서버사이드 렌더링 활성화 여부 (`true`/`false`).
        - 기본값은 `true`이며, `false`로 설정하면 클라이언트에서만 렌더링.
    - `loading`: 로딩 중 표시할 컴포넌트.


---
### 코드 예시

```tsx
const MonthlyEmissionsChart = dynamic(() => import("./MonthlyEmissionsChart"), {
  ssr: false, // 클라이언트에서만 렌더링
  loading: () => (
    <div className={styles.w100_box}>
      <div className={styles.w100}>
        <p className={styles.tit}>
          탄소배출량 현황 <span>&#40;단위 : CO2eq&#41;</span>
        </p>
        <div className={styles.chart_box}>
          Chart 데이터를 불러오고 있습니다.
        </div>
      </div>
    </div>
  ),
});

```

1. **`MonthlyEmissionsChart` 컴포넌트 동적 로드**
    
    - 해당 컴포넌트를 브라우저에서만 렌더링(`ssr: false`).
    - 서버사이드에서 이 컴포넌트는 렌더링되지 않으며, 클라이언트에서 로드됩니다.
    - 이는 차트 라이브러리(예: Chart.js)가 DOM에 의존적이거나 서버에서 제대로 작동하지 않을 때 유용합니다.
2. **로딩 중 UI 제공**
    
    - 컴포넌트가 로드되는 동안 `loading`으로 지정한 컴포넌트가 표시됩니다.
    - 예시에서는 "Chart 데이터를 불러오고 있습니다."라는 텍스트와 함께 스타일링된 로딩 UI를 표시.


---

### 사용 시점과 장점

#### 1. **클라이언트 전용 라이브러리 처리**

- 브라우저에서만 실행해야 하는 차트나 지도 라이브러리.
- 예: `Chart.js`, `Leaflet`, `Google Maps`.

#### 2. **초기 렌더링 속도 최적화**

- 페이지 로드 시 필요한 컴포넌트만 렌더링하고, 나머지는 동적으로 로드.

#### 3. **코드 스플리팅**

- 필요한 시점에만 컴포넌트를 로드하여 번들 크기를 줄이고 성능 개선.

---

### 동적 로드와 SSR 예제 비교

#### SSR 활성화 (기본값)
`const ComponentWithSSR = dynamic(() => import("./SomeComponent"));`
- 서버사이드에서 해당 컴포넌트를 렌더링하고, HTML에 포함.

#### SSR 비활성화
`const ComponentWithoutSSR = dynamic(() => import("./SomeComponent"), { ssr: false });`
- 서버에서는 렌더링하지 않고 클라이언트에서만 로드 및 렌더링.

---

### 요약

`dynamic`은 Next.js에서 동적 컴포넌트 로드를 처리하고, SSR 제어 및 로딩 상태 관리까지 제공합니다. `ssr: false` 설정은 클라이언트 전용 컴포넌트를 다룰 때 특히 유용하며, 로딩 중 UI를 커스터마이즈할 수 있습니다.