
#### HTML 코드에서 Import 하기

- 먼저, 차트를 포함한 페이지의 HTML 코드 상단에 적절한 `script` 로드를 포함해야 한다.
	- 현재, 수정된 프로젝트에서 `<script src="https://cdn.jsdelivr.net/npm/chart.js"></script> `와 같은 CDN을 로드하게 되면, 차트 데이터 구현을 위한 라이브러리 충돌이 발생한다. 
	- 다른 페이지에서, 차트 플러그인만 로드했다고 CDN 라이브러리를 추가로 로드하지 말고, 이제는 `chartjs-plugin-datalabels`만 로드하면 된다.
	- **만약**, 되던 차트가 안되는 경우, **캐시 버스트**를 의심할 수 있다.

```html
<link rel="stylesheet" type="text/css" href="/res/service/page/index/index.css?V=0029"/>  

%% import %%
<script src="/res/service/page/index/chartjs-plugin-datalabels@2.js?V=0032" type="text/javascript" charset="utf-8"></script> 

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
		    // ...
		    
		    // 차트정보를 관리함과 동시에, 페이지 내를 구성하는 자바스크립트 코드에서 언제나 접근할 수 있도록 page Triton 객체 하위 property로 구성
		    page.allCharts = []
	    }
	}
})
```

- `destory()` 메소드는 `canvas`에 그려진 차트의 내용을 지우는 역할을 한다. 
	- 이는 탭 전환으로 이미 그려진 `canvas`에 차트가 중복적으로 그려지는 문제를 방지한다.

```javascript
(function () {  
    return {  
        cssLoading: true,  
        htmlLoading: true,
	    onInit: function(j) {
		    // ...
		    page.allCharts = []
	    },
	    onChange: function (appendTo, parameterMap) {
	    // ...
	    },
	    detailPageOne: function (appendTo, parameterMap) {
	    // ...
	  
		if(page.allCharts){  
			page.allCharts.forEach(function(chart) {  
				chart.destroy();  
			});  
		  
			// 배열 비우기  
			page.allCharts = [];  
		}
	    }
	}
})
```

- 이제 Chart.js 의 사용 가이드에 따라 차트를 그리면 된다.
	- 이 때, 주의할 점은, `datalabels script` 로드가 완료되지 않으면 offset과 같은 `label set`이 이루어지지 않는다는 점이다.
	- 이는 HTML 파일에서 선언한 `script` 로드 이후에 이루어져야 하므로, 콜 스택과 Web API의 동작을 고려하여 차트 정의를 진행해야 한다.
	- 제일 간단하게 이를 고려하는 방법은 `setTimeout()`을 사용하는 것이다.
	- 아래는 사용 예시의 전체 코드이다.

```javascript
(function () {  
    return {  
  
        cssLoading: true,  
        htmlLoading: true,  
  
        onInit: function (j) {  
  
            var page = this;  
  
            var container = new Triton.Container({  
                appendTo: page.find('.student_home_page')  
            });  
  
            new Triton.Title({  
                appendTo: page.find('.page_title'),  
                content: PageConstructor.getCurrentMenuName()  
            }); 
            
            // 차트관련 정적 배열 식별자선언
            page.donutChart = [];  
            page.barCharts = [];  
  
  
           var tab = page.tab = new Triton.Tab({  
            // appendTo: container, 와 동일함  
               appendTo: page.find('.tap_area'),  
                theme : Triton.Tab.Theme.Normal,  
                contextmenuDisabled : true,  
                addButtonHidden: true,  
                listButtonHidden: true,  
                moveButtonHidden: true,  
                readOnly : false,  
                css : {'margin-top' : '20px'},  
  
                // 탭 버튼 추가될 때  
                onAdd: function (options) {  
                },  
  
                // 탭 선택 되엇을 때  
                onBind: function (options, index) {  
                    PageManager.cpcpm({index : index});  
                    page.tab.setTabIndex(PageManager.pcd(0, 'index'), true);  
                }  
            });  
            page.tab.addTabButton({  
                name : "1차 년도 - 2022년",  
            }, true, true);  
            page.tab.addTabButton({  
                name : "2차 년도 - 2023년",  
            }, true, true);  
            page.tab.addTabButton({  
                name : "3차 년도 - 2024년",  
            }, true, true);  
            page.tab.addTabButton({  
                name : "4차 년도 - 2025년",  
            }, true, true);  
            page.tab.addTabButton({  
                name : "5차 년도 - 2026년",  
            }, true, true);  
            page.tab.addTabButton({  
                name : "6차 년도 - 2027년",  
            }, true, true);  
  
  
            page.tab.setTabIndex(PageManager.pcd(0, 'index'), true);  
  
            var searchSection = new Triton.Section({  
                appendTo: page.find('.tap_area')  
            });  
  
            var searchPanel = new Triton.Panel({  
                appendTo: searchSection,  
            });  
  
            var categorySection = page.categorySection = new Triton.Section({  
                theme: Triton.Section.Theme.Category,  
                appendTo: searchPanel,  
                css: {'margin-top': '20px', 'margin-bottom' : '10px'}  
            });  
  
            new Triton.FlatButton({  
                appendTo: categorySection,  
                content: '엑셀 다운로드',  
                theme: Triton.FlatButton.Theme.Normal,  
                css: {'border-radius': '10px', 'float': 'right', 'margin-right': '5px'},  
                page: page,  
                onClick: function (e) {  
                    var page = e.data.page;  
	            });  
  
            new Triton.FlatButton({  
                appendTo: categorySection,  
                content: '추가',  
                theme: Triton.FlatButton.Theme.Normal,  
                css: {'border-radius': '10px', 'float': 'right', 'margin-right': '5px'},  
                page: page,  
                onClick: function (e) {  
                    var page = e.data.page;  
                    if(page.list != null){  
                        AjaxPopupManager.show(ProjectPopupUrl.ADD_ANNUAL_PERFORMANCE_AND_STATISTICS, {  
                            item : page.list[0],  
                            onSaved : function () {  
                                page.onChange();  
                            }  
                        });  
                    }else{  
                        AjaxPopupManager.show(ProjectPopupUrl.ADD_ANNUAL_PERFORMANCE_AND_STATISTICS, {  
                            onSaved : function () {  
                                page.onChange();  
                            }  
                        });  
                    }  
                }  
            });  
  
        },  
  
        onChange: function (jPage, jPageContainer, i, parameterMap, beforeParameterMap) {  
  
            var page = this;  
  
            Requester.func(function () {  
  
                var pageIndex = PageManager.pcd(0, 'index');  
                    page.detailPageOne(page.find('.tap_area'), parameterMap);  
            });  
        },  
  
        detailPageOne: function (appendTo, parameterMap) {  
  
            var page = this;  
            var year = parseInt(1)+parseInt(PageManager.pcd(0, 'index'))  
            var map = FormatHelper.arrayKeyToCamel(Triton.extractFormData(page.get()));  
            map['year'] = year;  
  
            Requester.awb(ProjectApiUrl.Project.GET_ANNUAL_PERFORMANCE_AND_STATISTICS_LIST, map , function (status, data, request) {  
  
                LoadingPopupManager.hide();  
  
                if (status != Requester.Status.SUCCESS) {  
                    return;  
                }  
  
                var list = page.list = Lia.p(data, 'body', 'list');  
  
                for(var idx in list) {  
  
                    var item = list[idx];  
  
                    var year = Lia.pd('0', item, 'year');  
                    var businessBudgetExecutionRate = Lia.pd('0', item, 'business_budget_execution_rate');  
                    var businessBudgetExecutionRateBar = Number(businessBudgetExecutionRate);  
                    if(businessBudgetExecutionRateBar >100){  
                        businessBudgetExecutionRateBar = 100;  
                    }  
                    var corePerformanceIndicatorAchievementRate = Lia.pd('0', item, 'core_performance_indicator_achievement_rate');  
                    var corePerformanceIndicatorAchievementRateBar = Number(corePerformanceIndicatorAchievementRate);  
                    if(corePerformanceIndicatorAchievementRateBar >100){  
                        corePerformanceIndicatorAchievementRateBar = 100;  
                    }  
                    var autonomousePerformanceIndicatorArchievementRate = Lia.pd('0', item, 'autonomous_performance_indicator_achievement_rate');  
                    var autonomousePerformanceIndicatorArchievementRateBar = Number(autonomousePerformanceIndicatorArchievementRate);  
                    if(autonomousePerformanceIndicatorArchievementRateBar >100){  
                        autonomousePerformanceIndicatorArchievementRateBar = 100;  
                    }  
  
                    var ad = Lia.pd('-', item, 'ad');  
                    var pbl = Lia.pd('-', item, 'pbl');  
                    var capstoneDesign = Lia.pd('-', item, 'capstone_design');  
                    var standardFieldTraining = Lia.pd('-', item, 'standard_field_training');  
                    var entrepreneurshipCourse = Lia.pd('-', item, 'entrepreneurship_course');  
                    var graduatedStudentsCount = Lia.pd('-', item, 'graduated_students_count');  
  
                    var yearString = '';  
                    var selectYearClassName = '';  
  
                    switch(year){  
  
                        case 1:{  
                            yearString = '2022';  
                            selectYearClassName = '.chart_box_y2022';  
  
                            break;  
                        }  
                        case 2:{  
                            yearString = '2023';  
                            selectYearClassName = '.chart_box_y2023';  
  
                            break;  
                        }  
                        case 3:{  
                            yearString = '2024';  
                            selectYearClassName = '.chart_box_y2024';  
                            break;  
                        }  
                        case 4:{  
                            yearString = '2025';  
                            selectYearClassName = '.chart_box_y2025';  
                            break;  
                        }  
                        case 5:{  
                            yearString = '2026';  
                            selectYearClassName = '.chart_box_y2026';  
                            break;  
                        }  
                        case 6:{  
                            yearString = '2027';  
                            selectYearClassName = '.chart_box_y2027';  
                            break;  
                        }  
  
                    }  
  
                    page.find('.graph-box__index .date').html(yearString + '.03 ~ ' + ++yearString + '.03');  
                    page.find('.graph-box__index .year').html(year + '차 년도');  
  
 
                    page.find('.edu-box li:eq(0) .list-num').html(ad);  
                    page.find('.edu-box li:eq(1) .list-num').html(pbl);  
                    page.find('.edu-box li:eq(2) .list-num').html(capstoneDesign);  
                    page.find('.edu-box li:eq(3) .list-num').html(standardFieldTraining);  
                    page.find('.edu-box li:eq(4) .list-num').html(entrepreneurshipCourse);  
                    page.find('.edu-box li:eq(5) .list-num').html(graduatedStudentsCount);  


					// 차트를 그리기 전, barCharts와 donutChart의 
                    if(page.barCharts){  
                        page.barCharts.forEach(function(chart) {  
                            chart.destroy();  
                        });  
  
                        // 배열 비우기  
                        page.barCharts = [];  
                    }  
                    if(page.donutChart){  
                        page.donutChart.forEach(function(chart) {  
                            chart.destroy();  
                        });  
  
                        // 배열 비우기  
                        page.donutChart = [];  
                    }  
                    // Bar  
                    setTimeout(()=>{  
                        let barChart = new Chart(page.find(selectYearClassName), {  
                            plugins: [ChartDataLabels],  
                            type: 'bar',  
                            data: {  
                                labels: ['AD', 'PBL', '캡스톤디자인', '표준현장실습','창업교과'],  
                                datasets: [  
                                    {  
                                        label: '일머리교육지수(명)',  
  
                                    data: [ad,pbl,capstoneDesign,standardFieldTraining,entrepreneurshipCourse],  
                                    borderRadius: 30,  
                                    backgroundColor:"#00377e",  
                                    datalabels: {  
                                        anchor: 'end',  
                                        align: 'start',  
  
                                        font: {  
                                            size: 18,  
                                            weight: 'bold',  
                                            color:"#000"  
                                        },  
                                    }  
                                },  
  
                            ],  
                        },  
                        options: {  
                            responsive: true,  
                            plugins: {  
                                datalabels: {  
                                    // color: '#fff',  
                                    // borderWidth: 2,                                    // borderColor: '#fff',                                    offset: -30,  
                                    formatter: function(value, context) {  
                                        return value;  
                                    },  
  
                                }  
                            }  
                        }  
                    });  
  
                    page.barCharts.push(barChart)  
                },500)  
  
  
                    const businessBudgetExecutionRateDoughnutData= businessBudgetExecutionRate == 100 ?  
                            [100,0] : businessBudgetExecutionRate == 0 ? [0, 100] :  
                            [businessBudgetExecutionRate, 100 - businessBudgetExecutionRate];  
                    const businessBudgetExecutionRateDoughnutBgColorData= businessBudgetExecutionRate == 100 ?  
                        ["green", "gray"] : businessBudgetExecutionRate == 0 ? ["green", "gray"]:  
                            ["green", "gray"];  
  
                    const corePerformanceIndicatorAchievementRateDoughnutData = corePerformanceIndicatorAchievementRate == 100 ?  
                        [100, 0] : corePerformanceIndicatorAchievementRate == 0 ? [0, 100] :  
                            [corePerformanceIndicatorAchievementRate, 100 - corePerformanceIndicatorAchievementRate];  
                    const corePerformanceIndicatorAchievementRateDoughnutBgColorData= businessBudgetExecutionRate == 100 ?  
                        ["#ff8000", "gray"] : businessBudgetExecutionRate == 0 ?["#ff8000", "gray"] :  
                            ["#ff8000", "gray"];  
  
                    const autonomousePerformanceIndicatorArchievementRateDoughnutData = autonomousePerformanceIndicatorArchievementRate == 100 ?  
                        [100, 0] : autonomousePerformanceIndicatorArchievementRate == 0 ? [0, 100] :  
                            [autonomousePerformanceIndicatorArchievementRate, 100 - autonomousePerformanceIndicatorArchievementRate];  
                    const autonomousePerformanceIndicatorArchievementRateDoughnutBgColorData= businessBudgetExecutionRate == 100 ?  
                        ["red", "gray"]: businessBudgetExecutionRate == 0 ? ["red", "gray"]:  
                            ["red", "gray"];  
  
                    // Doughnut  
                    setTimeout(()=>{  
                        let doughnutChart1 = new Chart(page.find('.doughnut-box1'), {  
                            plugins: [ChartDataLabels],  
                            type: 'doughnut',  
                            data: {  
                                labels: ['사업예산 집행률(%)'],  
                                datasets: [  
                                    {  
                                        label: '사업예산 집행률(%)',  
                                        data: businessBudgetExecutionRateDoughnutData,  
                                        backgroundColor:businessBudgetExecutionRateDoughnutBgColorData  
                                    },  
  
                                ],  
                            },  
                            options: {  
                                responsive: false,  
                                plugins: {  
                                    datalabels: {  
                                        color: '#fff',  
                                        // borderWidth: 2,  
                                        // borderColor: '#fff',                                        offset: 10,  
                                        font:{  
                                            size: 22  
                                        },  
                                        formatter: function(value, context) {  
                                            if(value == 0) {  
                                                return ""  
                                            }  
                                            return value + "%";  
                                        },  
  
                                    }  
                                }  
                            }  
                        });  
                        let doughnutChart2 = new Chart(page.find('.doughnut-box2'), {  
                            plugins: [ChartDataLabels],  
                            type: 'doughnut',  
                            data: {  
                                labels: ['핵심성과지표 달성률(%)'],  
                                datasets: [  
                                    {  
                                        label: '핵심성과지표 달성률(%)',  
                                        data: corePerformanceIndicatorAchievementRateDoughnutData,  
                                        backgroundColor:corePerformanceIndicatorAchievementRateDoughnutBgColorData  
                                    },  
  
                                ],  
                            },  
                            options: {  
                                responsive: false,  
                                plugins: {  
                                    datalabels: {  
                                        color: "#fff",  
                                        // borderWidth: 2,  
                                        // borderColor: '#fff',                                        offset: 10,  
                                        font:{  
                                            size: 22  
                                        },  
                                        formatter: function(value, context) {  
                                            if(value == 0) {  
                                                return ""  
                                            }  
                                            return value + "%";  
                                        },  
  
                                    }  
                                }  
                            }  
                        });  
                        let doughnutChart3 = new Chart(page.find('.doughnut-box3'), {  
                            plugins: [ChartDataLabels],  
                            type: 'doughnut',  
                            data: {  
                                labels: ['자율성과지표 달성률(%)'],  
                                datasets: [  
                                    {  
                                        label: '자율성과지표 달성률(%)',  
                                        data: autonomousePerformanceIndicatorArchievementRateDoughnutData,  
                                        backgroundColor:autonomousePerformanceIndicatorArchievementRateDoughnutBgColorData  
                                    },  
  
                                ],  
                            },  
                            options: {  
                                responsive: false,  
                                plugins: {  
                                    datalabels: {  
                                        color: '#fff',  
                                        // borderWidth: 2,  
                                        // borderColor: '#fff',                                        offset: 10,  
                                        font:{  
                                            size: 22  
                                        },  
                                        formatter: function(value, context) {  
                                            if(value == 0) {  
                                                return ""  
                                            }  
                                            return value + "%";  
                                        },  
  
                                    }  
                                }  
                            }  
                        });  
                        page.donutChart.push(doughnutChart1)  
                        page.donutChart.push(doughnutChart2)  
                        page.donutChart.push(doughnutChart3)  
                    },500)  
  
  
                }  
            });  
        },  
  
        onRelease: function (j) {  
        }    };  
})();
```