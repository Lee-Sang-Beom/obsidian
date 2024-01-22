
#### 사용에 앞서..

- 해당 프로젝트는 Chart.js 라이브러리를 script로 불러오긴 하였으나 사용하지 못한 것으로 보이는데 그 이유는 아래와 같다.
	- 페이지 로드 후, 얻은 데이터로 차트를 그려야하는데 IIFE 특성을 고려한 **비동기 코드**가 작성되지 않았다.
	- Chart.js 사용에 대한 스크립트 로드가 완벽하지 않다.

#### HTML 코드에서 Import 하기

- 먼저, 차트를 포함한 페이지의 HTML 코드 상단에 적절한 `script` 로드를 포함해야 한다.
	- 코드 상단 `script` 태그 2개를 그대로 복사 및 붙여넣기 하면 된다.
	- **`Chart.js`와 `ChartDataLabels` 플러그인이 이미 로드되어있는 상태에서 IIFE 코드가 스크립트에 접근**해야하기 때문에 필수적이다.

```html
<link rel="stylesheet" type="text/css" href="/res/service/page/index/index.css?V=0029"/>  
<!-- 여기 : 로드문제 수정-->  
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>  
<script src="/res/service/page/index/chartjs-plugin-datalabels@2.js?V=0031" type="text/javascript" charset="utf-8"></script>  
<style>  
    .statistics-frame {  
        clear: both;  
        width: 100%;  
    }  
    .graph-box {  
        display: flex;  
        align-items: stretch;  
        width: 100%;  
    }  
    .graph-box__index {  
        display: flex;  
        justify-content: flex-end;  
        flex-direction: column;  
        background-color: #00377e;  
        min-width: 320px;  
        max-width: 320px;  
        padding: 50px;  
        box-sizing: border-box;  
    }  
    .graph-box__index .date {  
        color: #fff;  
        font: 16px 'notokr-regular';  
    }  
    .graph-box__index .year {  
        padding-top: 20px;  
        color: #fff;  
        font: 40px 'notokr-bold';  
    }  
    .graph-box__list {  
        display: flex;  
        align-items: stretch;  
        gap: 40px;  
        min-width: calc(100% - 320px);  
        max-width: calc(100% - 320px);  
        padding: 30px 40px;  
        box-sizing: border-box;  
        background-color: #f2f6fa;  
    }  
    .graph-box__list>li {  
        flex: 0 0 calc(100%/3 - 40px/3*2);  
        padding: 35px 30px;  
        box-sizing: border-box;  
        background-color: #fff;  
    }  
    .graph-box__list>li .list-title {  
        display: flex;  
        justify-content: space-between;  
    }  
    .graph-box__list>li .list-title p {  
        color: #616161;  
        font: 20px 'notokr-medium';  
    }  
    .graph-box__list>li .list-title img {  
        display: inline-block;  
        width: 32px;  
        height: 32px;  
    }  
    .graph-box__list>li .list-con {  
        padding-top: 48px;  
    }  
    .list-con__percent {  
        text-align: right;  
    }  
    .list-con__percent .list-num {  
        color: #000;  
        font: 30px 'notokr-bold';  
    }  
    .list-con__percent span {  
        display: inline-block;  
        color: #5d5d5d;  
        font: 16px 'notokr-regular';  
    }  
    .list-con__percent.color .list-num {  
        color: #0077f9;  
    }  
    .graph-box__list>li .list-bar {  
        position: relative;  
        width: 100%;  
        height: 20px;  
        margin-top: 20px;  
        background-color: #f7f7f9;  
        box-sizing: border-box;  
    }  
    .graph-box__list>li .list-bar div {  
        position: absolute;  
        left: 0;  
        top: 0;  
        width: 10%;  
        height: 100%;  
        background-color: #000;  
    }  
    .graph-box__list>li .list-bar>div.blue {  
        background-color: #50b4f6;  
    }  
    .graph-box__list>li .list-bar>div.pink {  
        background-color: #ff778d;  
    }  
    .graph-box__list>li .list-bar>div.yellow {  
        background-color: #ffb931;  
    }  
    .edu-box {  
        padding-top: 30px;  
    }  
    .edu-box__title {  
        position: relative;  
        padding: 0 0 20px 16px;  
        color: #0c4a98;  
        font: 16px 'notokr-medium';  
    }  
    .edu-box__title::before {  
        content: '';  
        position: absolute;  
        left: 0;  
        top: 11px;  
        width: 2px;  
        height: 2px;  
        background-color: #094897;  
    }  
    .edu-box__list {  
        display: flex;  
        align-items: stretch;  
        gap: 20px;  
    }  
    .edu-box__list>li {  
        flex: 0 0 calc(100%/5 - 20px/5*5);  
        padding: 25px 30px;  
        box-sizing: border-box;  
        background-color: #f7f7f9;  
    }  
    .edu-box__list>li>p {  
        color: #5d5d5d;  
        font: 20px 'notokr-regular';  
    }  
  
    .chart_box{  
        width: 100%;  
        height: 100%;  
        margin-top: 30px;  
    }  
    .chart_box canvas{  
        display: none;  
    }  
  
    @media all and (max-width: 1560px) {  
        .graph-box__list {  
            gap: 20px;  
        }  
        .graph-box__list>li {  
            flex: 1 0 calc(100%/3 - 40px/3*2);  
        }  
        .graph-box__list>li .list-title p,  
        .edu-box__list>li>p {  
            font-size: 16px;  
        }  
    }  
</style>  
<div class="student_home_page">  
    <div class="page_title"></div>  
    <div class="tap_area"></div>  
<div class="statistics-frame" style="">  
    <div class="graph-box">  
        <div class="graph-box__index">  
            <p class="date">-</p>  
            <p class="year">-차 년도</p>  
        </div>  
        <ul class="graph-box__list">  
            <li>  
                <div class="list-title">  
                    <p>사업예산집행률</p>  
                    <img src="/res/icon_won.png" alt="사업예산집행률">  
                </div>  
                <div class="list-con">  
                    <div class="list-con__percent">  
                        <span class="list-num ratio"></span> <span>%</span>  
                    </div>  
                    <div class="list-bar">  
                        <div class="blue bubble"></div>  
                    </div>  
                </div>  
            </li>  
            <li>  
                <div class="list-title">  
                    <p>핵심성과지표 달성률</p>  
                    <img src="/res/icon_think.png" alt="핵심성과지표 달성률">  
                </div>  
                <div class="list-con">  
                    <div class="list-con__percent">  
                        <span class="list-num ratio"></span> <span>%</span>  
                    </div>  
                    <div class="list-bar">  
                        <div class="pink bubble"></div>  
                    </div>  
                </div>  
            </li>  
            <li>  
                <div class="list-title">  
                    <p>자율성과지표 달성률</p>  
                    <img src="/res/icon_goal.png" alt="자율성과지표 달성률">  
                </div>  
                <div class="list-con">  
                    <div class="list-con__percent">  
                        <span class="list-num ratio"></span> <span>%</span>  
                    </div>  
                    <div class="list-bar">  
                        <div class="yellow bubble"></div>  
                    </div>  
                </div>  
            </li>  
        </ul>  
    </div>  
   
<!--    <div class="edu-box">-->  
<!--        <div class="edu-box__title">일머리교육지수</div>-->  
<!--        <ul class="edu-box__list">-->  
<!--            <li>-->  
<!--                <p>AD</p>-->  
<!--                <div class="list-con__percent color">-->  
<!--                    <span class="list-num work-num"></span> <span>명</span>-->  
<!--                </div>-->  
<!--            </li>-->  
<!--            <li>-->  
<!--                <p>PBL</p>-->  
<!--                <div class="list-con__percent color">-->  
<!--                    <span class="list-num work-num"></span> <span>명</span>-->  
<!--                </div>-->  
<!--            </li>-->  
<!--            <li>-->  
<!--                <p>캡스톤디자인</p>-->  
<!--                <div class="list-con__percent color">-->  
<!--                    <span class="list-num work-num"></span> <span>명</span>-->  
<!--                </div>-->  
<!--            </li>-->  
<!--            <li>-->  
<!--                <p>표준현장실습</p>-->  
<!--                <div class="list-con__percent color">-->  
<!--                    <span class="list-num work-num"></span> <span>명</span>-->  
<!--                </div>-->  
<!--            </li>-->  
<!--            <li>-->  
<!--                <p>창업교과</p>-->  
<!--                <div class="list-con__percent color">-->  
<!--                    <span class="list-num work-num"></span> <span>명</span>-->  
<!--                </div>-->  
<!--            </li>-->  
<!--            <li style="display: none">-->  
<!--                <p>이수학생 수</p>-->  
<!--                <div class="list-con__percent color">-->  
<!--                    <span class="list-num work-num"></span> <span>명</span>-->  
<!--                </div>-->  
<!--            </li>-->  
<!--        </ul>-->  
<!--    </div>-->  
  

    <div class="chart_box">  
        <canvas class="chart_box_y2022" width="300" height="100"></canvas>  
        <canvas class="chart_box_y2023" width="300" height="100"></canvas>  
        <canvas class="chart_box_y2024" width="300" height="100"></canvas>  
        <canvas class="chart_box_y2025" width="300" height="100"></canvas>  
        <canvas class="chart_box_y2026" width="300" height="100"></canvas>  
        <canvas class="chart_box_y2027" width="300" height="100"></canvas>  
    </div>  
</div>  
</div>
```


#### 자바스크립트 코드에서 사용하기

- IIFE로 자바스크립트를 구성하는 해당 프로젝트는 반드시 차트를 포함해야 하는 페이지를 만들어야 한다면, IIFE 코드 최상단에 `htmlLoading`값을 true로 입력해야 한다.
 ```javascript
(function () {  
    return {  
        cssLoading: true,  
        htmlLoading: true,
	}
})
```

- `onInit` 함수에서는, 페이지 내에서 사용되는 모든 차트에 대한 접근이 가능하도록 차트 정보를 가지는 식별자를 만들어줘야 한다. 
	- 이는 탭 이동에 따른 `canvas` 구성에 따른 충돌을 방지하기 위함으로, 배열로 관리할 수 있다

```javascript

(function () {  
    return {  
        cssLoading: true,  
        htmlLoading: true,
	    onInit: function(j) {
		    ...
		// 차트정보를 관리함과 동시에, 페이지 내를 구성하는 자바스크립트 코드에서 ㅈ
		    page.allCharts = []
	    }
	}
})
```