- React의 주요 목표 중 하나는 성능 최적화이며, 특히 사용자가 느끼는 로딩 시간을 줄이는 것이다.
	- 이를 달성하기 위해 `React.lazy`와 `Suspense`는 코드 스플리팅(Code Splitting)과 동적 로딩(Dynamic Loading)을 쉽게 구현할 수 있도록 도와주는 강력한 도구로서 제공되고 있다.


#### 1. React.lazy

- `React.lazy`는 컴포넌트를 동적으로 로드할 수 있도록 도와주는 React API이다. 동적 로딩은 애플리케이션의 크기를 줄이고, 필요한 시점에만 해당 컴포넌트를 로드하여 초기 로딩 시간을 최적화할 수 있도록 한다.

##### ※ 사용법
- `React.lazy`를 사용하면 동적으로 컴포넌트를 로드할 수 있다.
	- 일반적으로 `import` 문은 컴파일 시점에 모든 코드를 한 번에 번들로 묶지만, `React.lazy`를 사용하면 특정 컴포넌트를 별도의 청크(chunk)로 분리해 필요할 때 로드하도록 설정할 수 있다.

```tsx
import React, { Suspense } from 'react';

// React.lazy를 이용한 동적 컴포넌트 로딩
const MyComponent = React.lazy(() => import('./MyComponent'));

function App() {
  return (
    <div>
      <h1>React.lazy Example</h1>
      <Suspense fallback={<div>Loading...</div>}>
        <MyComponent />
      </Suspense>
    </div>
  );
}

export default App;
```
##### ※ 특징
- `React.lazy`는 `import()`를 반환하는 함수(즉, _dynamic import_)와 함께 사용된다.
	- 참고로, Next.js 프레임워크에서는 기본적으로 코드 스플리팅과 동적 로딩을 지원하므로, `React.lazy` 대신 `next/dynamic`을 사용하는 것이 일반적이다.

- 로드된 컴포넌트는 `React.Component`처럼 사용할 수 있다.
- 동적 로딩이 이루어지므로, 컴포넌트가 사용되기 전까지는 네트워크 요청이 발생하지 않는다.

---
#### 2. Suspense

- `Suspense`는 `React.lazy`와 함께 사용되는 컴포넌트로, 동적 로딩 중 컴포넌트가 준비되지 않은 상태에서 대체 UI를 **렌더링**한다. 이를 통해 사용자 경험을 개선할 수 있다.

##### ※ 사용법
- `Suspense` 컴포넌트는 `fallback` 속성을 사용하여 로딩 중에 표시할 콘텐츠를 정의하는데, `React.lazy`로 로드하는 모든 컴포넌트는 반드시 `Suspense`로 감싸야 한다.

```tsx
<Suspense fallback={<div>Loading...</div>}>
  <MyLazyComponent />
</Suspense>
```
##### ※ 특징
- `fallback` 속성은 로딩 중 보여줄 JSX를 정의한다.
- 컴포넌트 로드가 완료되면 자동으로 실제 컴포넌트가 렌더링된다.
- 여러 컴포넌트를 로드할 때 각각의 `Suspense`를 사용할 수도 있고, 하나의 `Suspense`로 묶을 수도 있다.

---
#### 3. React.lazy와 Suspense 사용 예시

```tsx
"use client";
import style from "./home.module.scss";
import HomeComplaintsAnalysis from "./(home)/HomeComplaintsAnalysis";
import HomeFinancialStatus from "./(home)/HomeFinancialStatus";
import HomePopularSearchTerms from "./(home)/HomePopularSearchTerms";
import { lazy, Suspense } from "react";

import { ClctDataListPageResponse } from "@/types/clctData/clctDataType";
import Link from "next/link";

const HomeWeather = lazy(() => import("./(home)/HomeWeather"));
const HomeMap = lazy(() => import("./(home)/HomeMap"));
const HomeUpdatedData = lazy(() => import("./(home)/HomeUpdatedData"));
const HomeKeyMetrics = lazy(() => import("./(home)/HomeKeyMetrics"));

interface IProps {}

export default function Home({}: IProps) {
  return (
    <div className={style.wrap}>
      <div className={style.wrap_scroll}>
        <div className={style.m_slogan}>
          <p>
            <span>꿈</span>이 이루어지는
          </p>
          <p>
            <span>따뜻한 행복도시</span> 김해
          </p>
        </div>
        <div className={style.character}>
          <img src="/img/home/icon_character.png" alt="깃발 든 토덕이 캐릭터" />
        </div>
        {/* MAP - 완료 */}
        <Suspense
          fallback={
            <div className={style.map_loading}>
              <img
                src="/img/home/img-loading-and-drawing-map.svg"
                alt="토더기 일러스트 날개짓 아이콘"
              />
              <p>데이터를 불러오고 있습니다...</p>
            </div>
          }
        >
          <HomeMap />
        </Suspense>
        <div className={style.con_l_box}>
          {/* 날씨 - 완료 */}
          <Suspense
            fallback={
              <div className={style.weather_data}>
                <p className={style.tit}>
                  <span>날씨</span>
                </p>
                <div className={`${style.weather_box} ${style.loading_box}`}>
                  날씨 데이터를 불러오고 있습니다...
                </div>
              </div>
            }
          >
            <Link
              href="https://www.weather.go.kr/w/weather/forecast/short-term.do#dong/4825053000/35.22567/128.88882/%EA%B2%BD%EC%83%81%EB%82%A8%EB%8F%84%20%EA%B9%80%ED%95%B4%EC%8B%9C%20%EB%B6%80%EC%9B%90%EB%8F%99/SEL/%EB%B6%80%EC%9B%90%EB%8F%99"
              title="새 창으로 열기 : 기상청 날씨누리 페이지 이동"
              target="_blank"
              prefetch={false}
            >
              <HomeWeather />
            </Link>
          </Suspense>

          {/* 민원분석 */}
          <Link title="민원분석 페이지 이동" href="/anl/aComplaint">
            <HomeComplaintsAnalysis />
          </Link>

          {/* 재정현황 */}
          <HomeFinancialStatus />
        </div>
        <div className={style.con_r_box}>
          {/* 갱신데이터 - 완료 */}
          <Suspense
            fallback={
              <div className={style.update_data}>
                <p className={style.tit}>갱신데이터</p>
                <div className={style.update_box}>
                  <p
                    style={{
                      width: "100%",
                      textAlign: "center",
                      fontWeight: 400,
                    }}
                  >
                    갱신데이터 목록을 불러오는 중입니다...
                  </p>
                </div>
              </div>
            }
          >
            <HomeUpdatedData />
          </Suspense>

          {/* 주요지표 - 완료 */}
          <Suspense
            fallback={
              <div className={style.indicator_data}>
                <p className={style.tit}>주요지표</p>
                <div className={style.indicator_loading_desc_box}>
                  주요지표에 표시할 <br />
                  데이터 목록을 불러오는 중입니다...
                </div>
              </div>
            }
          >
            <HomeKeyMetrics />
          </Suspense>

          {/* 인기검색어 - 완료 */}
          <HomePopularSearchTerms />
        </div>
      </div>
    </div>
  );
}
```

---

#### 4. React.lazy와 Suspense를 활용한 코드 스플리팅의 장점

1. **최적화된 번들 크기**:
    - 초기 로딩 시 사용되지 않는 컴포넌트를 나중에 로드함으로써 번들 크기를 줄일 수 있다.

2. **빠른 사용자 인터랙션**:
    - 초기 렌더링 속도가 빨라지고, 사용자와의 상호작용에 필요한 리소스만 로드된다.

3. **코드 관리 용이성**:
    - 모듈화된 코드를 통해 유지보수성과 가독성을 높일 수 있다.
