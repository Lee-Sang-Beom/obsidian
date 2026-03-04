
### 1. 정리 이전

- TSX 파일을 작성하고, 이와 연결된 module css파일을 만든다음 이 둘을 연결시키면서 그동안 느꼈던 점이 무지성으로 코드를 작성하면 굉장히 css파일을 한눈에 보기 불편해지는 상황이 온다는 것이었다.
- 이를 줄이기 위해 어떻게 해야할까 고민했다.

#### ※ 정리되지 않은 `tsx` 코드

```tsx
"use client";
import style from "./home.module.scss";
import HomeComplaintsAnalysis from "./(home)/HomeComplaintsAnalysis";
import HomeFinancialStatus from "./(home)/HomeFinancialStatus";
import HomePopularSearchTerms from "./(home)/HomePopularSearchTerms";
import { lazy, Suspense } from "react";

import { ClctDataListPageResponse } from "@/types/clctData/clctDataType";
import Link from "next/link";

// HomeWeather 컴포넌트를 React.lazy로 동적 로딩
const HomeWeather = lazy(() => import("./(home)/HomeWeather"));
const HomeMap = lazy(() => import("./(home)/HomeMap"));
const HomeUpdatedData = lazy(() => import("./(home)/HomeUpdatedData"));
const HomeKeyMetrics = lazy(() => import("./(home)/HomeKeyMetrics"));

interface IProps {}

export default function Home({}: IProps) {
  return (
    <div className={style.wrap}>
      <div className={style.wrap_scroll}>
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

#### ※ 정리되지 않은 `css` 코드
```css
.wrap {
  width: 100%;
  height: 880px;
  padding: 20px 0;
  box-sizing: border-box;
  position: relative;
  display: inline-block;
  background: #edf0fa url("/img/home/map_bg.svg") no-repeat top center;
  // background:
  //   linear-gradient(180deg, #edf0fa 0%, #f4f9fd 100%),
  //   url("/img/main/map_bg.svg") no-repeat top center;

  // background-blend-mode: multiply;
  background-size: 1920px auto;
}
.map_box {
  // opacity: 0.5;
  position: absolute;
  top: 115px;
  left: calc(50% - 350px);
  // left: 595px;
  .map_con {
    width: 700px;
    // height: 467px;
    // text-shadow: 0.849px 0.849px 0px rgba(0, 0, 0, 0.25);
  }
  .map_con svg {
    width: 100%;
    height: 100%;
    position: relative;
    z-index: 2;
    display: block;
    visibility: visible !important;
    g,
    g a,
    g a path {
      z-index: 2;
      display: block;
      visibility: visible !important;
    }
  }

  .dong {
    stroke: #fff;
    stroke-width: 3;
    stroke-opacity: 0.5;
    cursor: pointer;
    transition: all 0.5s ease;
  }
  .map_con a:hover {
    z-index: 10000;
    position: relative;
    order: 0;
  }
  .map_con a:hover .dong {
    stroke: #123388;
    stroke-width: 10px;
    stroke-opacity: 1;
    transition: all 0.5s ease;
    // fill: #123388;
  }
  .text {
    fill: #111;
    font-size: var(--fs-30);
    font-weight: 500;
    text-anchor: middle;
    cursor: pointer;
  }
  // .text.active {
  //   fill: #fff;
  // }
  // .map_con a:hover .text {
  //   fill: #fff;
  // }

  // 지도배경색 (레벨에 따른)
  .level1 {
    fill: #cee7fa;
  }
  .level2 {
    fill: #b1daff;
  }
  .level3 {
    fill: #8fc9ff;
  }
  .level4 {
    fill: #6dadf8;
  }
  .level5 {
    fill: #3e86f3;
  }

  .map_hover {
    position: absolute;
    z-index: 2;
    width: 300px;
    height: fit-content;
    padding: 10px 10px 5px 10px;
    // left: calc(50% + 150px);
    // right: calc(0% - 120px);
    // bottom: 0;

    background-color: #fff;
    border-radius: 8px;
    border: 1px solid #d3d3d3;
    p {
      border-bottom: solid 1px #666;
      padding-bottom: 12px;
      span {
        display: inline-block;
      }
      span:nth-of-type(1) {
        border-radius: 3px;
        background: #123388;
        color: #fff;
        padding: 6px 12px;
        font-size: var(--fs-16);
        font-weight: 600;
      }
      span:nth-of-type(2) {
        font-size: var(--fs-14);
        font-weight: 600;
        float: right;
        padding-top: 15px;
      }
    }
    .list li {
      border-bottom: dashed 1px #d3d3d3;
      padding: 8px 0;
      span:nth-of-type(1) {
        font-size: var(--fs-16);
        font-weight: 600;
      }
      span:nth-of-type(2) {
        font-size: var(--fs-16);
        font-weight: 500;
        float: right;
      }
    }
    .list li:nth-last-of-type(1) {
      border-bottom: none;
    }
    .color {
      display: flex;
      max-width: 100% !important;
      li {
        width: 25%;
        font-size: var(--fs-12);
        padding: 7px 0;
        text-align: center;
      }
      li:nth-of-type(1) {
        background-color: #9bb7ff;
        border-radius: 3px 0 0 3px;
      }
      li:nth-of-type(2) {
        background-color: #ffc369;
      }
      li:nth-of-type(3) {
        background-color: #ff97a5;
      }
      li:nth-of-type(4) {
        background-color: #bef3ff;
        border-radius: 0 3px 3px 0;
      }
    }
    .legend {
      margin: 10px 0 5px 50px;
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      li {
        float: left;
        font-size: var(--fs-14);
        position: relative;
        padding-left: 15px;
        margin-right: 12px;
      }
    }
    .legend li::before {
      content: "";
      position: absolute;
      top: 2px;
      left: 0;
      width: 10px;
      height: 10px;
      border-radius: 2px;
    }
    .legend li:nth-of-type(1)::before {
      background-color: #9bb7ff;
    }
    .legend li:nth-of-type(2)::before {
      background-color: #ffc369;
    }
    .legend li:nth-of-type(3)::before {
      background-color: #ff97a5;
    }
    .legend li:nth-of-type(4)::before {
      background-color: #bef3ff;
    }
  }

  .map_legend {
    position: absolute;
    width: 190px;
    bottom: -60px;
    left: calc(50% - 95px);
    span {
      float: left;
      padding: 4px 5px;
      font-size: var(--fs-14);
      font-weight: 500;
    }
    ul li {
      width: 22px;
      height: 22px;
      float: left;
    }
  }
}
.map_pop {
  position: absolute;
  width: 300px;
  top: 50px;
  // padding: 10px 10px 5px 10px;
  // left: calc(50% + 150px);
  right: 500px !important;
  // background-color: #fff;
  border-radius: 8px;
  // border: 1px solid #d3d3d3;
  p {
    border-bottom: solid 1px #d3d3d3;
    padding-bottom: 12px;
    span {
      display: inline-block;
    }
    span:nth-of-type(1) {
      color: #123388;
      font-size: var(--fs-16);
      font-weight: 600;
    }
    span:nth-of-type(2) {
      font-size: var(--fs-12);
      font-weight: 400;
      float: right;
      padding-top: 5px;
      color: var(--gray-700);
    }
  }
  ul li {
    border-bottom: dashed 1px #d3d3d3;
    padding: 8px 0;
    span:nth-of-type(1) {
      font-size: var(--fs-14);
      font-weight: 500;
    }
    span:nth-of-type(2) {
      font-size: var(--fs-14);
      font-weight: 400;
      float: right;
    }
  }
  // ul li:nth-last-of-type(1) {
  //   border-bottom: none;
  // }
}
.map_legend ul li:nth-of-type(1) {
  background-color: #cee7fa;
}
.map_legend ul li:nth-of-type(2) {
  background-color: #b1daff;
}
.map_legend ul li:nth-of-type(3) {
  background-color: #8fc9ff;
}
.map_legend ul li:nth-of-type(4) {
  background-color: #6dadf8;
}
.map_legend ul li:nth-of-type(5) {
  background-color: #3e86f3;
}
.con_l_box {
  width: 400px;
  float: left;
  display: flex;
  flex-direction: column;
  gap: 30px;
  padding-left: 50px;
  .weather_data {
    width: 100%;
    display: inline-block;
  }
  .complaint_data {
    width: 100%;
    display: inline-block;
  }
  .finance_data {
    width: 100%;
    display: inline-block;
  }
}
.weather_data,
.complaint_data,
.finance_data {
  .tit {
    span:nth-of-type(1) {
      font-size: var(--fs-18);
      font-weight: 600;
    }
    span:nth-of-type(2) {
      font-size: var(--fs-12);
      font-weight: 400;
      float: right;
      margin-top: 5px;
      color: var(--gray-700);
    }
  }
}

.weather_data {
  .weather_box {
    margin-top: 10px;
    padding: 20px 10px 10px 10px;
    border-radius: 8px;
    border: 1px solid#D3D3D3;
    background: #fff;

    &.loading_box {
      display: flex;
      height: 100px;
      padding: 10px;
      align-items: center;
      justify-content: center;
    }
    .weather_top {
      width: 100%;
      display: inline-block;
      .weather_icon {
        float: left;
        text-align: center;
        padding-left: 30%;
        img {
          width: 40px;
          height: 40px;
          // margin-left: 10px;
        }
        p {
          font-size: var(--fs-14);
          font-weight: 500;
          margin-top: 5px;
        }
      }
      .weather_info {
        float: left;
        padding-left: 5%;
        p:nth-of-type(1) {
          font-size: var(--fs-30);
          font-weight: 600;
          color: #123388;
          text-align: center;
        }
        p:nth-of-type(2) {
          margin-top: 15px;
          font-size: var(--fs-16);
          font-weight: 600;
          text-align: center;
          span:nth-of-type(1) {
            color: #5a87fa;
          }
          span:nth-of-type(2) {
            color: #e7455c;
          }
        }
      }
    }
    ul {
      padding: 10px 5px;
      margin-top: 10px;
      border-radius: 3px;
      background: #f3f5f8;
      width: 100%;
      box-sizing: border-box;
      display: inline-block;
      li {
        float: left;
        width: 25%;
        position: relative;
        text-align: center;
        p:nth-of-type(1) {
          font-size: var(--fs-14);
          font-weight: 500;
        }
        p:nth-of-type(2) {
          font-size: var(--fs-16);
          font-weight: 600;
          color: #123388;
          padding-top: 10px;
        }
      }
      li::after {
        content: "";
        position: absolute;
        top: 25%;
        right: 0;
        width: 1px;
        height: 25px;
        background-color: #d3d3d3;
      }
      li:nth-last-of-type(1)::after {
        display: none;
      }
    }
  }
}
.complaint_box {
  margin-top: 10px;
  box-sizing: border-box;
  // padding: 30px 10px 0 10px;
  padding: 10px 10px 10px 10px;
  border-radius: 8px;
  border: 1px solid#D3D3D3;
  background: #fff;
  height: 264px;
  position: relative;
}
.chart_box {
  width: 100%;
  height: 205px;
}
.chart_bot {
  position: absolute;
  bottom: 10px;
  right: 10px;
  color: #666;
  font-size: var(--fs-14);
  font-weight: 400;
  text-align: right;
}
.finance_box {
  margin-top: 10px;
  box-sizing: border-box;
  padding: 20px 10px 10px 10px;
  border-radius: 8px;
  border: 1px solid#D3D3D3;
  background: #fff;
  height: 264px;
  ul {
    width: 100%;
    display: inline-block;
    border-radius: 3px;
    background: #f3f5f8;
    padding: 12px 5px;
    box-sizing: border-box;
    li::after {
      content: "";
      position: absolute;
      top: -5px;
      right: 0;
      width: 1px;
      height: 50px;
      background-color: #d3d3d3;
    }
    li:nth-last-of-type(1)::after {
      display: none;
    }
  }
  ul li {
    float: left;
    position: relative;
    width: calc(100% / 3);
    text-align: center;
    p:nth-of-type(1) {
      font-size: var(--fs-14);
      font-weight: 500;
    }
    p:nth-of-type(2) {
      font-size: var(--fs-18);
      font-weight: 600;
      margin-top: 10px;
    }
  }
}
.finance_box ul li:nth-of-type(1) p:nth-of-type(2) {
  color: #123388;
}
.finance_box ul li:nth-of-type(2) p:nth-of-type(2) {
  color: #e7455c;
}
.finance_box ul li:nth-of-type(3) p:nth-of-type(2) {
  color: #5a87fa;
}
.con_r_box {
  width: 400px;
  float: right;
  display: flex;
  flex-direction: column;
  gap: 30px;
  padding-right: 50px;
  .update_data {
    width: 100%;
    display: inline-block;
  }
  .indicator_data {
    width: 100%;
    display: inline-block;
  }
  .search_data {
    width: 100%;
    display: inline-block;
  }
}
.update_data,
.indicator_data,
.search_data {
  .tit {
    font-size: var(--fs-18);
    font-weight: 600;
  }
}
.update_box {
  width: 100%;
  height: 40px;
  overflow: hidden;
  box-sizing: border-box;
  border-radius: 8px;
  border: 1px solid #d3d3d3;
  background: #fff;
  padding: 10px 15px;
  margin-top: 10px;
  position: relative;

  span.newIcon {
    width: 16px;
    height: 16px;

    position: absolute;
    top: 50%;
    left: 9px;
    transform: translateY(-50%);
    display: inline-block;
    text-align: center;
    font-size: var(--fs-14);
    padding-top: 2px;
    box-sizing: border-box;
    color: #fff;
    border-radius: 1px;
    background: #d50136;
  }
  p {
    font-size: var(--fs-16);
    font-weight: 400;

    span {
      position: relative;
      top: 2px;
    }
    span:nth-of-type(1) {
      display: inline-block;

      margin-left: 17px;
      max-width: calc(100% - 120px);
      white-space: nowrap; /* 한 줄로 표시 */
      overflow: hidden; /* 넘치는 부분 숨김 */
      text-overflow: ellipsis; /* 넘칠 때 '...'로 생략 */
    }
    span:nth-of-type(2) {
      float: right;
    }
  }
}
.indicator_data {
  .indicator_loading_desc_box {
    width: 100%;
    height: 420px;
    margin-top: 10px;
    background: #fff;
    border-radius: 8px;
    border: 1px solid #d3d3d3;
    line-height: 140%;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
  }
  ul {
    width: 100%;
    flex-wrap: wrap;
    display: flex;
    gap: 9px;
    margin-top: 10px;
  }
  ul li {
    width: calc((100% / 3) - 6px);
    box-sizing: border-box;
    text-align: center;
    a {
      padding: 12px 0;
      border-radius: 8px;
      border: 1px solid #d3d3d3;
      background: #fff;
    }
    a:hover {
      border: 1px solid #123388;
      box-shadow: 0px 0px 10px 0px rgba(17, 17, 17, 0.25);
    }
    p:nth-of-type(1) {
      text-align: center;
      img {
        width: 25px;
        height: 25px;
        margin: 0 auto;
      }
    }
    p:nth-of-type(2) {
      font-size: var(--fs-16);
      font-weight: 500;
      color: #123388;
      margin-top: 5px;
    }
    p:nth-of-type(3) {
      font-size: var(--fs-12);
      font-weight: 400;
      margin-top: 5px;
      line-height: 120%;
      color: var(--gray-700);
    }
  }
}
.search_box {
  width: 100%;
  box-sizing: border-box;
  border-radius: 8px;
  border: 1px solid #d3d3d3;
  background: #fff;
  padding: 15px 15px 5px 15px;
  margin-top: 10px;
  table {
    width: 100%;
    thead {
      border-radius: 3px;
      background: #f3f5f8;
      padding: 6px 0;
    }
    th {
      font-size: var(--fs-14);
      font-weight: 500;
      color: var(--gray-800);
      padding: 10px 0;
      p {
        font-size: var(--fs-12);
        font-weight: 400;
        color: var(--gray-700);
        margin-top: 3px;
      }
    }
    td {
      font-size: var(--fs-14);
      font-weight: 400;
      color: var(--gray-700);
      text-align: center;
      border-bottom: solid 1px #d3d3d3;
      padding: 10px 0;
    }
    tr:nth-last-of-type(1) td {
      border-bottom: none;
    }
  }
}

/*반응형*/
// @media only screen and (min-width: 641px) and (max-width: 1919px) {
//   .map_pop {
//     right: calc(0% + 20px) !important;
//   }
// }
@media only screen and (min-width: 641px) and (max-width: 1640px) {
  .con_l_box,
  .con_r_box {
    width: 350px !important;
  }
  .map_pop {
    right: 450px !important ;
  }
}
@media only screen and (min-width: 641px) and (max-width: 1523px) {
  .wrap {
    overflow-x: auto;
    // background-image: none;
  }
  .wrap_scroll {
    width: 100%;
    min-width: 1523px;
    position: relative;
  }
  .map_box {
    left: 411.5px !important;
  }
  .map_pop {
    top: 30px !important;
  }
}
@media only screen and (max-width: 640px) {
  .wrap {
    height: auto !important;
    background-image: none !important;
  }
  .map_box {
    position: relative !important;
    top: 74px !important;
    left: 0 !important;
    right: 0;
    .map_con {
      width: 100%;
    }
    .map_hover {
      display: none;
    }

    .map_legend {
      display: none;
      // bottom: -30px;
    }
  }
  .map_pop {
    width: calc(100% - 20px) !important;
    box-sizing: border-box;
    position: relative !important;
    top: auto !important;
    left: 10px !important;
    right: 10px !important;
    margin-top: 100px;
    p span:nth-of-type(1) {
      font-size: var(--fs-16);
    }
    p span:nth-of-type(2) {
      font-size: var(--fs-12);
    }
    ul li span {
      font-size: var(--fs-14) !important;
    }
  }
  .con_l_box,
  .con_r_box {
    clear: both !important;
  }
  .con_l_box {
    margin-top: 30px;
    padding-left: 10px;
    width: calc(100% - 20px) !important;
  }
  .con_r_box {
    margin-top: 30px;
    padding-right: 10px;
    width: calc(100% - 20px) !important;
  }
  .weather_data,
  .complaint_data,
  .finance_data {
    .tit {
      span:nth-of-type(1) {
        font-size: var(--fs-16);
      }
      span:nth-of-type(2) {
        font-size: var(--fs-12);
      }
    }
  }

  .weather_icon p {
    font-size: var(--fs-12) !important;
  }
  .weather_box ul li p:nth-of-type(2) {
    font-size: var(--fs-12) !important;
  }
  .weather_box ul li::after,
  .finance_box ul li::after {
    display: none;
  }
  .chart_bot {
    font-size: var(--fs-12) !important;
  }
  .finance_box ul li p:nth-of-type(1),
  .finance_box ul li p:nth-of-type(2) {
    font-size: var(--fs-12) !important;
  }
  .update_data,
  .indicator_data,
  .search_data {
    .tit {
      font-size: var(--fs-16) !important;
    }
  }
  .update_box p {
    font-size: var(--fs-14) !important;
  }
  .indicator_data ul li p:nth-of-type(1) svg {
    width: 18px;
    height: 18px;
  }
  .indicator_data ul li p:nth-of-type(2) {
    font-size: var(--fs-14) !important;
  }
  .indicator_data ul li p:nth-of-type(3) {
    font-size: var(--fs-12) !important;
  }
  .indicator_data ul {
    gap: 5px !important;
  }
  .indicator_data ul li {
    width: calc((100% / 3) - (10px / 3));
  }
  .search_box table tr th,
  .search_box table tr td {
    font-size: var(--fs-12) !important;
  }
}

@media only screen and (max-width: 400px) {
  .weather_icon {
    padding-left: 20% !important;
  }
  .indicator_data ul li a p {
    word-break: break-all;
  }
}

// loading
.map_loading {
  display: none;
}

```

### 2. 정리 이후

- CSS-IN-CSS 방식을 사용하는 본 회사에서는 일단 화면 별 위치(SECTION 별)나 화면 별 위치를 세부적으로 구성하기 껄끄러운 상황인 경우 포함하는 기능을 한 눈에 확인할 수 있도록 파일 및 주석을 명명하는 것이 크게 도움이 된다는 것을 느꼈다.

- 그래서 프론트엔드 회의 때, 아래와 같은 이미지처럼 CSS 구조를 정의하거나 컴포넌트명을 구성하자는 제안을 진행했다.
	- 결과적으로, 긴 코드를 보고 "유지보수"를 진행할 때, 해당 화면의 영역을 수정하기 위해 어느 부분을 찾아가야하고 어떻게 수정해야하는지에 대한 탐색과정 측면에서 굉장한 보완이 이루어졌다.

#### ※ 정리된 `css` 코드 (1)
![[css(1).png]]
![[css(2).png]]

#### ※ 정리된 `tsx` 코드 (1)
![[tsx(1).png]]
![[tsx(2).png]]

#### ※ 정리된 `css` 코드 (2)
![[css(3).png]]

#### ※ 정리된 `tsx` 코드 (2)
![[tsx(3).png]]

#### ※ 한 페이지를 구성하기 위한 모듈 구성 예시 (Section별, 기능별 분리) 
![[컴포넌트, 모듈 css 파일 함께 분리.png]]