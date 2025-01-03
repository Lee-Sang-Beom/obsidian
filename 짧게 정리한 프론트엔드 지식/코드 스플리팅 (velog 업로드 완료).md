
#### 1.  코드 스플리팅

- 코드 스플리팅(Code Splitting)은 웹 애플리케이션에서 코드를 작은 청크(chunk)로 분리하여 애플리케이션의 초기 로딩 속도를 개선하는 기술이다. 
- 이 기술은 번들 파일을 여러 개로 나누고, **사용자가 필요로 하는 부분만 로드함으로써 네트워크 비용과 사용자 대기 시간을 줄이는 데 효과적**이다.

---
#### 2. 코드 스플리팅의 필요성

- SPA(Single Page Application)와 같은 현대 웹 애플리케이션은 모든 코드를 하나의 번들로 묶으면 초기 로딩 시간이 길어지게 되는 문제가 발생할 수 있다.
	- 아래는 모든 코드를 하나의 번들로 묶었을 때 발생할 수 있는 문제와 해결방안이다.

- **문제점**
	- 모든 코드가 한 번에 로드되면 대규모 애플리케이션의 경우 초기 로딩이 느려짐
	- 사용자가 필요하지 않은 기능이나 페이지의 코드도 미리 로드됨

- **해결책**
	- 코드 스플리팅을 통해 필요한 시점에 필요한 코드만 로드하여 **최소한의 리소스**로 동작할 수 있도록 한다.

---
#### 3. 코드 스플리팅의 장점

- **초기 로딩 속도 개선**
    - 앱에서 반드시 필요한 코드만 먼저 로드하고, 나머지는 나중에 로드

- **사용자 경험 향상**
    - 사용자가 더 빠르게 페이지와 상호작용할 수 있도록 지원

- **효율적인 네트워크 사용**
    - 페이지별로 코드를 분리해 불필요한 리소스를 로드하지 않음

- **확장성**
    - 기능별 모듈화로 코드 관리 및 유지보수가 쉬워짐

---
#### 4. 코드 스플리팅 구현방법

###### 4-1. Webpack 기반 코드 스플리팅
1. Dynamic Import를 사용한 코드 스플리팅
	- Webpack의 `import()` 구문을 활용해 동적으로 모듈을 로드한다.
```js
function loadComponent() {
  import('./MyComponent').then((module) => {
    const MyComponent = module.default;
    // 모듈 사용
  });
}
```

2. Webpack 설정으로 자동 코드 스플리팅
	- Webpack 설정에서 `splitChunks` 옵션을 사용하면 청크 파일을 자동으로 생성할 수 있다.
```js
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all', // 모든 청크에 대해 스플리팅 수행
    },
  },
};
```

###### 4-2. React에서의 코드 스플리팅
1. React.lazy와 Suspense 사용
	- React는 `React.lazy`와 `Suspense`를 통해 컴포넌트를 동적 로드하고, 로드 중 상태를 표시할 수 있다.
```jsx
import React, { Suspense } from 'react';

// React.lazy : 동적으로 컴포넌트 로드
const MyComponent = React.lazy(() => import('./MyComponent'));

function App() {
  return (
    {/* Suspense : 로딩 중일 때 fallback 속성으로 전달한 element를 표시한다. */}
    <Suspense fallback={<div>Loading...</div>}>
      <MyComponent />
    </Suspense>
  );
}
```

2. React Router와의 코드 스플리팅
	- React Router와 결합하여 페이지 단위로 코드를 스플리팅할 수 있다.
```jsx
import React, { Suspense } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

const Home = React.lazy(() => import('./Home'));
const About = React.lazy(() => import('./About'));

function App() {
  return (
    <Router>
      <Suspense fallback={<div>Loading...</div>}>
        <Switch>
          <Route path="/" exact component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </Suspense>
    </Router>
  );
}
```

###### 4-3. Next.js에서의 코드 스플리팅
1. `dynamic` 함수
	- Next.js는 기본적으로 코드 스플리팅을 지원하며, `dynamic` 함수를 통해 추가 제어가 가능하다.
	- Next.js는 서버 사이드 렌더링(SSR)과 클라이언트 사이드 렌더링(CSR) 모두에서 코드 스플리팅을 지원한다.
```jsx
import dynamic from 'next/dynamic';

// 동적 로드 설정
const DynamicComponent = dynamic(() => import('./MyComponent'), {
  loading: () => <p>Loading...</p>, // 로딩 중 표시할 컴포넌트
});

function App() {
  return <DynamicComponent />;
}
```

> 실제 필자가 작성해본 `dynamic` 함수를 사용한 스플리팅
```tsx
"use client";

import dynamic from "next/dynamic";
import styles from "./DashboardClient.module.scss";
import { User } from "next-auth";
import Loading from "@/components/common/Loading/Loading";

interface IProps {
  user: User;
}
const MonthlyEmissionsChart = dynamic(() => import("./MonthlyEmissionsChart"), {
  ssr: false,
  loading: () => (
    <>
      <Loading text="탄소배출량 현황 정보를 불러오고 있습니다." open={true} />
      <div className={styles.w100_box}>
        <div className={styles.w100}>
          <div className={styles.tit}>
            <p>
              탄소배출량 현황 <span>&#40;단위 : CO2eq&#41;</span>
            </p>
          </div>
          <div className={styles.chart_box}>
            차트 데이터를 불러오고 있습니다...
          </div>
        </div>
      </div>
    </>
  ),
});
const ScopeEmissionsChart = dynamic(() => import("./ScopeEmissionsChart"), {
  ssr: false,
  loading: () => (
    <>
      <Loading
        text="Scope별 탄소배출량 현황 정보를 불러오고 있습니다."
        open={true}
      />
      <div className={styles.w30}>
        <div className={styles.tit}>
          <p>
            Scope별 탄소배출량 현황 <span>&#40;단위 : CO2eq&#41;</span>
          </p>
        </div>
        <div className={styles.chart_box}>
          차트 데이터를 불러오고 있습니다...
        </div>
      </div>
    </>
  ),
});
const ProcessEmissionsChart = dynamic(() => import("./ProcessEmissionsChart"), {
  ssr: false,
  loading: () => (
    <>
      <Loading
        text="공정별 탄소배출량 현황 정보를 불러오고 있습니다."
        open={true}
      />
      <div className={styles.w30}>
        <div className={styles.tit}>
          <p>
            공정별 탄소배출량 현황 <span>&#40;단위 : CO2eq&#41;</span>
          </p>
        </div>
        <div className={styles.chart_box}>
          차트 데이터를 불러오고 있습니다...
        </div>
      </div>
    </>
  ),
});
const SourceEmissionsChart = dynamic(() => import("./SourceEmissionsChart"), {
  ssr: false,
  loading: () => (
    <>
      <Loading
        text="배출원별 탄소배출량 현황 정보를 불러오고 있습니다."
        open={true}
      />
      <div className={styles.w30}>
        <div className={styles.tit}>
          <p>
            배출원별 탄소배출량 현황 <span>&#40;단위 : CO2eq&#41;</span>
          </p>
        </div>
        <div className={styles.chart_box}>
          차트 데이터를 불러오고 있습니다...
        </div>
      </div>
    </>
  ),
});
export default function DashboardClient({ user }: IProps) {
  return (
    <div className={styles.wrap}>
      {/* 탄소배출량 현황 */}
      <MonthlyEmissionsChart user={user} />

      <div className={styles.w100_box}>
        {/* Scope별 탄소배출량 현황 */}
        <ScopeEmissionsChart user={user} />

        {/* 공정별 탄소배출량 현황 */}
        <ProcessEmissionsChart user={user} />

        {/* 배출원별 탄소배출량 현황 */}
        <SourceEmissionsChart user={user} />
      </div>
    </div>
  );
}
```

---
#### 5. 코드 스플리팅을 적용 가능한 사례

1. **대규모 애플리케이션**: 여러 페이지나 기능별로 분리된 번들을 로드.
2. **SPA 환경**: 라우트 단위로 스플리팅하여 초기 로드 시간 최소화.
3. **모바일 앱**: 느린 네트워크 환경에서 효율적 로딩을 위한 최적화.