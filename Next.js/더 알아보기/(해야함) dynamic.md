
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


> DashboardClient.tsx
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

> MonthlyEmissionsChart.tsx
```tsx
"use client";

import * as echarts from "echarts";
import "./chart.css";
import styles from "./DashboardClient.module.scss";
import Selectbox, {
  SelectboxType,
} from "@/components/common/Selectbox/Selectbox";
import { useEffect, useLayoutEffect, useRef, useState } from "react";
import { User } from "next-auth";
import { SelectChangeEvent } from "@mui/material";
import { SearchGraphInfoCommand } from "@/types/areas/inventory/command/SearchGraphInfoCommand";
import { useGetGraphView } from "@/hooks/areas/inventory/useGetGraphView";
import {
  baseSeriesLabelOption,
  baseSeriesOption,
  baseXAxisLabelOption,
  baseYAxisBaseOption,
  eChartBaseOption,
  getYAxisMaxNumber,
} from "@/utils/common/chart/eChartUtils";
import Loading from "@/components/common/Loading/Loading";

interface IProps {
  user: User;
}

interface ChartSeries {
  label: string;
  value: number[];
}
export default function MonthlyEmissionsChart({ user }: IProps) {
  const chartRef = useRef<HTMLDivElement>(null);

  /**
   * @name yearList
   * @description 2000 ~ 현재년도 데이터
   *
   * @name command
   * @type SearchGraphInfoCommand
   * @description 차트데이터 조회를 위한 command
   *
   * @name useGetGraphView
   * @type GraphViewDataResponse
   * @description 차트 데이터 query
   */
  const [yearList, setYearList] = useState<SelectboxType[]>([]);
  const [command, setCommand] = useState<SearchGraphInfoCommand>({
    graphType: "MONTHLY_EMISSIONS",
    orgBoundarySeq: user.boundarySystemSeq,
    year: new Date().getFullYear().toString(),
  });
  const { isLoading, data: chartData } = useGetGraphView(command);

  /**
   * @name useLayoutEffect
   * @description 연도 선택 화면 구성
   */
  useLayoutEffect(() => {
    const currentYear = new Date().getFullYear(); // 현재 연도

    const from2000ToCurYearList = Array.from(
      { length: currentYear - 2000 + 1 }, // 길이 = 현재 연도 - 시작 연도 + 1 (그래야 24년도기준 2000~2024 총 25개 길이가 나옴)
      (_, i) => 2000 + i // 시작 연도에서 순차적으로 값 추가 (0 ~ 24)
    );

    setYearList(
      from2000ToCurYearList
        .sort((a, b) => b - a)
        .map((y) => {
          return {
            name: y.toString() + "년",
            value: y.toString(),
            group: "",
          };
        })
    );
  }, []);

  /**
   * @name useEffect
   * @description 차트 데이터 세팅
   */
  useEffect(() => {
    const chartDom = chartRef.current;
    let mngChart: any;

    const chartDataValue: ChartSeries[] =
      chartData &&
      chartData.length > 0 &&
      chartData[0].ebarData &&
      chartData[0].ebarData.datasets
        ? chartData[0].ebarData.datasets.map((dataSet) => {
            return {
              label: dataSet.label,
              value: dataSet.data,
              //   value: [0, 0, 500, 0, 0, 0, 0, 0, 0, 0, 0, 0],
            };
          })
        : [];

    const chartColorList: string[] =
      chartData &&
      chartData.length > 0 &&
      chartData[0].ebarData &&
      chartData[0].ebarData.datasets
        ? chartData[0].ebarData.datasets.map((dataSet) => {
            return dataSet.backgroundColor[0];
          })
        : [];

    const chartXAxisData =
      chartData &&
      chartData.length > 0 &&
      chartData[0].ebarData &&
      chartData[0].ebarData.labels
        ? chartData[0].ebarData.labels
        : [];

    if (chartDom) {
      mngChart = echarts.getInstanceByDom(chartDom);
      if (!mngChart) {
        mngChart = echarts.init(chartDom);
      }

      let option: echarts.EChartsOption = {
        ...eChartBaseOption,
        color: chartColorList,
        xAxis: {
          type: "category",
          axisLabel: baseXAxisLabelOption,
          data: chartXAxisData,
        },
        yAxis: {
          ...baseYAxisBaseOption,
        },
        // @ts-ignore
        series: chartDataValue.map((seriesItem: ChartSeries) => {
          return {
            ...baseSeriesOption,
            name: seriesItem.label,
            type: "bar",
            stack: "total",
            barWidth: "20%",
            data: seriesItem.value.map((val: number) => {
              return {
                value: val.toFixed(3),
                itemStyle: {
                  borderRadius: [0, 0, 0, 0],
                },
                label: {
                  ...baseSeriesLabelOption,
                  show: false,
                  formatter: `${val.toLocaleString()}`,
                },
              };
            }),
            yAxisIndex: 0,
          };
        }),
      };

      mngChart.setOption(option);
      window.addEventListener("resize", () => mngChart.resize());

      return () => {
        // 컴포넌트가 DOM에서 제거되기 직전에 실행
        mngChart.dispose(); // 생성된 차트 삭제하고 메모리 해제
        window.removeEventListener("resize", () => mngChart.resize());
      };
    }
  }, [chartData]);
  return (
    <div className={styles.w100_box}>
      <div className={styles.w100}>
        <div className={styles.tit}>
          <p>
            탄소배출량 현황 <span>&#40;단위 : CO2eq&#41;</span>
          </p>
          <div className={styles.year_selbox}>
            {yearList.length > 0 ? (
              <Selectbox
                items={yearList}
                placeholder="연도 선택"
                title="연도 선택"
                color="white"
                border="br_square_round_1"
                size="md"
                onChange={function (event: SelectChangeEvent): void {
                  setCommand((prev) => {
                    return {
                      ...prev,
                      year: event.target.value,
                    };
                  });
                }}
                value={command.year}
              />
            ) : (
              <></>
            )}
          </div>
        </div>
        <div className={styles.chart_box} id="bar_chart" ref={chartRef}></div>
      </div>
    </div>
  );
}
```