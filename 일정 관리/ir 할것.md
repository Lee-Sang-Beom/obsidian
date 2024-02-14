
- 통계분석 계속해서 상단라인이 추가되는 현상 해결필요

- 통계분석 
```javascript
(function () {  
  
    return {  
        htmlLoading: true,  
        cssLoading: true,  
  
        onInit: function (j) {  
  
            // 단과대학 불러오기  
            Requester.awb(  
                ProjectApiUrl.Project.GET_PARTICIPATION_DEPARTMENT_LIST,  
                {},  
                function (status, data, request) {  
                    if (status != Requester.Status.SUCCESS) {  
                        return;  
                    }  
  
                    const collegeSelectList = Lia.pd([], data, "body", "college_list");  
                    page.collegeList = Lia.pd([], data, "body");  
  
                    const filtedCollegeSelectList = collegeSelectList.map(  
                        (collegeItem) => {  
                            return {  
                                name: collegeItem.college_name,  
                                value: collegeItem.dept_cd,  
                            };  
                        },  
                    );  
  
                    page.collegeSelectList = [  
                        {name:"선택안함", value:""},  
                        ...filtedCollegeSelectList,  
                    ];  
                },  
            );  
  
            let page = this;  
  
            // selectBox 보이도록 하기  
            page.isShowSemester = false;  
            page.isShowMonth = false;  
            page.isShowEquip = false;  
  
            // 차트영역 관리하기  
            page.chart1 = [];  
            page.chart2 = [];  
            page.chartDataDisplayTable = null;  
  
  
            new Triton.Title({  
                appendTo: page.find('.page_title'),  
                content: PageConstructor.getCurrentMenuName(),  
            });  
  
            page.resultSection = new Triton.Section({ // 현황  
                appendTo:page.find('.page_wrap'),  
                css: {  
                    'padding' : '0 20px 20px 20px',  
                    'overflow' : 'hidden',  
                }  
            });  
  
        },  
        onChange: function (jPage, jPageContainer, i, parameterMap, beforeParameterMap) {  
  
            let page = this;  
  
            if(page.chartDataDisplayTable){  
                page.chartDataDisplayTable.empty();  
            }  
  
            Requester.func(function () {  
                page.resultSection.empty();  
                page.contentPage(page.resultSection, parameterMap);  
            });  
        },  
        contentPage: function (appendTo, parameterMap){  
            let searchSection = new Triton.Section({  
                appendTo: page.find('.search_wrap')  
            });  
            let searchPanel = new Triton.Panel({  
                appendTo: searchSection,  
                css: {'margin-bottom' : '20px', 'contentMinWidth': '900px',}  
            });  
            let searchDetailTable = new Triton.DetailTable({  
                appendTo: searchPanel  
            });  
  
            // 테이블 Row            searchDetailTable.appendRow({});  
  
            // 테이블 검색요소 추가 : 항목  
            searchDetailTable.appendKeyColumn({  
                content: '항목'  
  
            });  
  
            searchDetailTable.appendValueColumn({  
                content: page.searchItemType = new Triton.ComboBox({  
                    theme: Triton.ComboBox.Theme.Full,  
                    form: {name: 'searchItemType'},  
                    optionList: [  
                        // {  
                        //     name: '항목선택',  
                        //     value: 'a0'                        // },                        {  
                            name: '전임교원 참여비율(%)',  
                            value: 'a1'  
                        },  
                        {  
                            name: '교원업적평가 · 인사제도의 산학협력 실적 실제 반영률(%)',  
                            value: 'a2'  
                        },  
                        {  
                            name: '취업률(%)',  
                            value: 'a3'  
                        },  
                        {  
                            name: '표준현장실습학기제 이수학생 비율(%)',  
                            value: 'a4'  
                        },  
                        {  
                            name: '표준현장실습학기제 이수학생 취업률(%)',  
                            value: 'a5'  
                        },  
                        {  
                            name: '일반캡스톤 이수학생 비율(%)',  
                            value: 'a6'  
                        },  
                        {  
                            name: '창업교과 이수학생 비율(%)',  
                            value: 'a7'  
                        },  
                        {  
                            name: '기업협업센터(ICC) 운영수입(천원)',  
                            value: 'a8'  
                        },  
                        {  
                            name: '기업협업센터(ICC) 활동건수(건)',  
                            value: 'a9'  
                        },  
                        {  
                            name: '교수 1인당 산학공동 연구건수(건)',  
                            value: 'a10'  
                        },  
                        {  
                            name: '교수 1인당 산학공동 연구비(천원)',  
                            value: 'a11'  
                        },  
                        {  
                            name: '교수 및 학생 1인당 대학 창업건수(건)',  
                            value: 'a12'  
                        },  
                        {  
                            name: '교수 1인당 대학 창업기업 수익금(원)',  
                            value: 'a13'  
                        },  
                        {  
                            name: '교수 1인당 기술이전 계약건수(건)',  
                            value: 'a14'  
                        },  
                        {  
                            name: '교수 1인당 기술이전 수입료(천원)',  
                            value: 'a15'  
                        },  
                        {  
                            name: '기술이전 건당 수입료(천원)',  
                            value: 'a16'  
                        },  
                        {  
                            name: '교수 1인당 산학공동연구 지식재산권 창출건수',  
                            value: 'a17'  
                        },  
                        {  
                            name: '공용장비 활용기업수',  
                            value: 'a18'  
                        },  
                        {  
                            name: '공용장비 운영수입(천원)',  
                            value: 'a19'  
                        },  
                        {  
                            name: '교육과정 운영수',  
                            value: 'a20'  
                        },  
                        {  
                            name: '재직자 교육과정 이수자수',  
                            value: 'a21'  
                        }  
                    ],  
                    onSelected: function (val) {  
                        Requester.func(function () {  
  
                            // 차트 영역 및 데이터 초기화  
                            if(page.chart1){  
                                page.chart1.forEach(function(chart) {  
                                    chart.destroy();  
                                });  
  
                                // 배열 비우기  
                                page.chart1 = [];  
                            }  
                            if(page.chart2){  
                                page.chart2.forEach(function(chart) {  
                                    chart.destroy();  
                                });  
  
                                // 배열 비우기  
                                page.chart2 = [];  
                            }  
  
                            // 테이블 영역 초기화  
                            if(page.chartDataDisplayTable){  
                                page.chartDataDisplayTable.empty();  
                            }  
                            // 검색패널 영역 초기화  
                            if(page.find('searchPanel')){  
                                const keyColumn = page.find('.searchPanel')  
                                keyColumn.remove();  
                            }  
  
                            // 비교대학, 학과 체크  
                            if(page.find('.college1')){  
                                const keyColumn = page.find('.college1')  
                                keyColumn.parents('tr').remove();  
                            }  
                            if(page.find('.college2')){  
                                const keyColumn = page.find('.college2')  
                                keyColumn.parents('tr').remove();  
                            }  
                            if(page.find('.college3')){  
                                const keyColumn = page.find('.college3')  
                                keyColumn.parents('tr').remove();  
                            }  
                            if(page.find('.college4')){  
                                const keyColumn = page.find('.college4')  
                                keyColumn.parents('tr').remove();  
                            }  
  
                            // 학기 체크  
                            if(page.find('.key_column_semester')){  
                                page.hakgiComboBox=""  
                                const keyColumn = page.find('.key_column_semester')  
                                const valueColumn = page.find('.value_column_semester')  
                                keyColumn.parents('tr').remove();  
                            }  
  
                            // 월 체크  
                            if(page.find('.key_column_month')){  
                                page.monthComboBox=""  
                                const keyColumn = page.resultSection.find('.key_column_month')  
                                const valueColumn = page.resultSection.find('.value_column_month')  
                                keyColumn.parents('tr').remove();  
                            }  
  
                            // 장비명 체크  
                            if(page.find('.key_column_equip')){  
                                page.equipComboBox = ""  
                                const keyColumn = page.resultSection.find('.key_column_equip')  
                                keyColumn.parents('tr').remove();  
                            }  
  
                            setTimeout(()=>{  
                                switch (val) {  
                                    // 전임교원 참여비율(%)  
                                    case 'a1':  
                                        page.isShowMonth=false;  
                                        page.isShowSemester=false;  
                                        page.isShowEquip=false;  
  
                                        // 대학 1                                        searchDetailTable.appendRow({});  
                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'college1',  
                                            content: '비교대학1',  
  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.collegeComboBoxOne = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "daehak_cd1" },  
                                                optionList: page.collegeSelectList,  
                                                onSelected: function (val) {  
                                                    const totalDepartmentList = Lia.pd([], page.collegeList, "list");  
                                                    const filtedDepartmentList = totalDepartmentList  
                                                        .filter((departmentItem) => {  
                                                            return departmentItem.dept_up_cd.toString() == val.toString();  
                                                        })  
                                                        .map((departmentItem) => {  
                                                            return {  
                                                                name: departmentItem.department_name,  
                                                                value: departmentItem.dept_cd,  
                                                            };  
                                                        });  
  
                                                    // 여기 바꿔야함!  
                                                    page.departmentList1 = [  
                                                        {name:"선택안함", value:""},  
                                                        ...filtedDepartmentList,  
                                                    ];  
  
                                                    Requester.func(function () {  
                                                        // 관련학과 리스트 설정  
                                                        page.departmentComboBoxOne.setOptionList(page.departmentList1);  
  
                                                        // 만약 페이지에 department queryString이 존재하는 경우 초기값으로 설정  
                                                        page.departmentComboBoxOne.setValue(PageManager.pc("hakbu_cd1"));  
                                                    });  
                                                },  
                                            })),  
                                        });  
                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'department1',  
                                            content: '비교학과1',  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.departmentComboBoxOne = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "hakbu_cd1" },  
                                                optionList: page.departmentList1,  
                                                onSelected: function (val) {},  
                                            })),  
                                        });  
  
                                        // 대학 2                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'college2',  
                                            content: '비교대학2',  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.collegeComboBoxTwo = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "daehak_cd2" },  
                                                optionList: page.collegeSelectList,  
                                                onSelected: function (val) {  
                                                    const totalDepartmentList = Lia.pd([], page.collegeList, "list");  
                                                    const filtedDepartmentList = totalDepartmentList  
                                                        .filter((departmentItem) => {  
                                                            return departmentItem.dept_up_cd.toString() == val.toString();  
                                                        })  
                                                        .map((departmentItem) => {  
                                                            return {  
                                                                name: departmentItem.department_name,  
                                                                value: departmentItem.dept_cd,  
                                                            };  
                                                        });  
  
                                                    // 여기 바꿔야함!  
                                                    page.departmentList2 = [  
                                                        {name:"선택안함", value:""},  
                                                        ...filtedDepartmentList,  
                                                    ];  
                                                    Requester.func(function () {  
                                                        // 관련학과 리스트 설정  
                                                        page.departmentComboBoxTwo.setOptionList(page.departmentList2);  
  
                                                        // 만약 페이지에 department queryString이 존재하는 경우 초기값으로 설정  
                                                        page.departmentComboBoxTwo.setValue(PageManager.pc("hakbu_cd2"));  
                                                    });  
                                                },  
                                            })),  
                                        });  
                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'department2',  
                                            content: '비교학과2',  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.departmentComboBoxTwo = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "hakbu_cd2" },  
                                                optionList: page.departmentList2,  
                                                onSelected: function (val) {},  
                                            })),  
                                        });  
  
                                        // 대학 3                                        searchDetailTable.appendRow({});  
                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'college3',  
                                            content: '비교대학3',  
  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.collegeComboBoxThree = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "daehak_cd3" },  
                                                optionList: page.collegeSelectList,  
                                                onSelected: function (val) {  
                                                    const totalDepartmentList = Lia.pd([], page.collegeList, "list");  
                                                    const filtedDepartmentList = totalDepartmentList  
                                                        .filter((departmentItem) => {  
                                                            return departmentItem.dept_up_cd.toString() == val.toString();  
                                                        })  
                                                        .map((departmentItem) => {  
                                                            return {  
                                                                name: departmentItem.department_name,  
                                                                value: departmentItem.dept_cd,  
                                                            };  
                                                        });  
  
                                                    // 여기 바꿔야함!  
                                                    page.departmentList3 = [  
                                                        {name:"선택안함", value:""},  
                                                        ...filtedDepartmentList,  
                                                    ];  
  
                                                    Requester.func(function () {  
                                                        // 관련학과 리스트 설정  
                                                        page.departmentComboBoxThree.setOptionList(page.departmentList3);  
  
                                                        // 만약 페이지에 department queryString이 존재하는 경우 초기값으로 설정  
                                                        page.departmentComboBoxThree.setValue(PageManager.pc("hakbu_cd3"));  
                                                    });  
                                                },  
                                            })),  
                                        });  
                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'department3',  
                                            content: '비교학과3',  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.departmentComboBoxThree = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "hakbu_cd3" },  
                                                optionList: page.departmentList3,  
                                                onSelected: function (val) {},  
                                            })),  
                                        });  
  
                                        // 대학 4                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'college4',  
                                            content: '비교대학4',  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.collegeComboBoxFour = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "daehak_cd4" },  
                                                optionList: page.collegeSelectList,  
                                                onSelected: function (val) {  
                                                    const totalDepartmentList = Lia.pd([], page.collegeList, "list");  
                                                    const filtedDepartmentList = totalDepartmentList  
                                                        .filter((departmentItem) => {  
                                                            return departmentItem.dept_up_cd.toString() == val.toString();  
                                                        })  
                                                        .map((departmentItem) => {  
                                                            return {  
                                                                name: departmentItem.department_name,  
                                                                value: departmentItem.dept_cd,  
                                                            };  
                                                        });  
  
                                                    // 여기 바꿔야함!  
                                                    page.departmentList4 = [  
                                                        {name:"선택안함", value:""},  
                                                        ...filtedDepartmentList,  
                                                    ];  
                                                    Requester.func(function () {  
                                                        // 관련학과 리스트 설정  
                                                        page.departmentComboBoxFour.setOptionList(page.departmentList4);  
  
                                                        // 만약 페이지에 department queryString이 존재하는 경우 초기값으로 설정  
                                                        page.departmentComboBoxFour.setValue(PageManager.pc("hakbu_cd4"));  
                                                    });  
                                                },  
                                            })),  
                                        });  
                                        searchDetailTable.appendKeyColumn({  
                                            addClass: 'department4',  
                                            content: '비교학과4',  
                                        });  
                                        searchDetailTable.appendValueColumn({  
                                            css:{width:'200px'},  
                                            content: (page.departmentComboBoxFour = new Triton.ComboBox({  
                                                theme: Triton.ComboBox.Theme.Full,  
                                                form: { name: "hakbu_cd4" },  
                                                optionList: page.departmentList4,  
                                                onSelected: function (val) {},  
                                            })),  
                                        });  
  
                                        // 이렇게 세팅안해주면 대학콤보박스의 onSelected가 실행되지 않아, 비교학과들의 초기값 세팅이 안됨  
                                        page.collegeComboBoxOne.setValue(PageManager.pcd("", "daehak_cd1"));  
                                        page.collegeComboBoxTwo.setValue(PageManager.pcd("", "daehak_cd2"));  
                                        page.collegeComboBoxThree.setValue(PageManager.pcd("", "daehak_cd3"));  
                                        page.collegeComboBoxFour.setValue(PageManager.pcd("", "daehak_cd4"));  
  
                                        // 검색  
                                        let searchButtonSection = new Triton.ButtonSection({  
                                            appendTo: searchPanel,  
                                            addClass: 'searchPanel'  
                                        });  
  
                                        searchButtonSection.appendToRight(  
                                            new Triton.FlatButton({  
                                                content: "검색 조건 초기화",  
                                                theme: Triton.FlatButton.Theme.Normal,  
                                                page: page,  
                                                onClick: function (e) {  
                                                    let item = FormatHelper.arrayKeyToUnderscore(  
                                                        Triton.extractFormData(page.get()),  
                                                    );  
                                                    console.log("검색조건 초기화 전: ", item)  
  
                                                    for (let key in item) {  
                                                        item[key] = "";  
                                                    }  
                                                    console.log("검색조건 초기화 후: ", item)  
  
                                                    PageManager.cpcpm(item);  
                                                },  
                                            }),  
                                        );  
                                        searchButtonSection.appendToRight(  
                                            new Triton.FlatButton({  
                                                content: "검색",  
                                                theme: Triton.FlatButton.Theme.Normal,  
                                                page: page,  
                                                onClick: function (e) {  
                                                    let page = e.data.page;  
                                                    let item = FormatHelper.arrayKeyToUnderscore(  
                                                        Triton.extractFormData(page.get()),  
                                                    );  
                                                    console.log("검색: ", item)  
                                                    PageManager.cpcpm(item);  
                                                },  
                                            }),  
                                        );  
  
                                        // 차트 그리기  
                                        setTimeout(()=>{  
  
                                            const year = PageManager.pcd(2022, 'year');  
                                            console.log("year is ", year)  
                                            let barChart1 = new Chart(page.find('.chart_box1'), {  
                                                plugins: [ChartDataLabels],  
                                                type: 'bar',  
                                                data: {  
                                                    labels: ['데이터 1', '데이터 2', '데이터 3', '데이터 4'],  
                                                    datasets: [  
                                                        {  
                                                            label: ['데이터 1', '데이터 2', '데이터 3', '데이터 4'],  
                                                            // barThickness: 80,  
                                                            data: [70, 50, 40, 100],  
                                                            backgroundColor:['blue', 'orange', 'gray', 'purple']  
                                                        },  
  
                                                    ],  
                                                },  
                                                options: {  
                                                    responsive: false,  
                                                    plugins: {  
                                                        legend:{  
                                                            display: false,  
                                                        },  
                                                        datalabels: {  
                                                            color: '#fff',  
                                                            font:{  
                                                                size: 18  
                                                            },  
                                                            formatter: function(value, context) {  
                                                                if(value == 0) {  
                                                                    return ""  
                                                                }  
                                                                return value+"%";  
                                                            },  
  
                                                        }  
                                                    }  
                                                }  
                                            });  
                                            let barChart2 = new Chart(page.find('.chart_box2'), {  
                                                plugins: [ChartDataLabels],  
                                                type: 'line',  
                                                data: {  
                                                    labels: ['2021년', '2022년', '2023년'],  
                                                    datasets: [  
                                                        {  
                                                            label: ['2021년', '2022년', '2023년'],  
                                                            // barThickness: 80,  
                                                            data: [70, 50, 40, 100],  
                                                            backgroundColor:['blue', 'orange', 'gray', 'purple']  
                                                        },  
  
                                                    ],  
                                                },  
                                                options: {  
                                                    responsive: false,  
                                                    plugins: {  
                                                        legend:{  
                                                            display: false,  
                                                        },  
                                                        datalabels: {  
                                                            color: '#fff',  
                                                            font:{  
                                                                size: 18  
                                                            },  
                                                            formatter: function(value, context) {  
                                                                if(value == 0) {  
                                                                    return ""  
                                                                }  
                                                                return value+"%";  
                                                            },  
  
                                                        }  
                                                    }  
                                                }  
                                            });  
                                            page.chart1.push(barChart1);  
                                            page.chart2.push(barChart2);  
                                        },500)  
  
                                        // 테이블 영역 추가  
                                        let chartDataDisplayTable = page.chartDataDisplayTable = new Triton.DetailTable({  
                                            appendTo: page.find(".table_wrap"),  
                                        });  
  
                                        break;  
  
                                    case 'a6':  
                                    case 'a7':  
                                        page.isShowMonth=false;  
                                        page.isShowSemester =true;  
                                        page.isShowEquip=false;  
  
                                        // 항목 선택에 따라 연도에 추가되어야 할 요소: 학기  
                                        if(page.isShowSemester){  
                                            searchDetailTable.appendRow({});  
                                            searchDetailTable.appendKeyColumn({  
                                                addClass: 'key_column_semester',  
                                                content: '학기',  
                                            });  
                                            searchDetailTable.appendValueColumn({  
                                                content: page.hakgiComboBox = new Triton.ComboBox({  
                                                    addClass: 'value_column_semester',  
                                                    theme: Triton.ComboBox.Theme.Full,  
                                                    css: {'width': '150px'},  
                                                    form: {name: 'hakgi'},  
                                                    optionList: [  
                                                        {  
                                                            name: '학기선택',  
                                                            value: ''  
                                                        },  
                                                        {  
                                                            name: '1학기',  
                                                            value: 10  
                                                        },  
                                                        {  
                                                            name: '하계 계절학기',  
                                                            value: 11  
                                                        },  
                                                        {  
                                                            name: '2학기',  
                                                            value: 20  
                                                        },  
                                                        {  
                                                            name: '동계 계절학기',  
                                                            value: 21  
                                                        }  
                                                    ],  
                                                    onKeyPress: function (e) {  
                                                        if (e.which == Lia.KeyCode.ENTER) {  
                                                            page.searchButton.trigger('click');  
                                                        }  
                                                    }  
                                                })  
                                            });  
                                        }  
                                        break;  
                                    case 'a3':  
                                    case 'a10':  
                                    case 'a12':  
                                    case 'a13':  
                                    case 'a14':  
                                    case 'a15':  
                                    case 'a16':  
                                        page.isShowMonth=true;  
                                        page.isShowSemester=false;  
                                        page.isShowEquip=false;  
  
                                        // 항목 선택에 따라 연도에 추가되어야 할 요소: 월  
                                        if(page.isShowMonth){  
                                            // 테이블 검색요소 추가 : 학기  
                                            searchDetailTable.appendRow({})  
                                            searchDetailTable.appendKeyColumn({  
                                                addClass: 'key_column_month',  
                                                content: '월',  
                                            });  
                                            searchDetailTable.appendValueColumn({  
                                                content: page.monthComboBox = new Triton.ComboBox({  
                                                    theme: Triton.ComboBox.Theme.Full,  
                                                    addClass: 'value_column_month',  
                                                    form: {name: 'month'},  
                                                    css : {'width':'150px'},  
                                                    optionList: [  
                                                        {name: '월 선택', value: ''},  
                                                        {name: '1월', value: 1},  
                                                        {name: '2월', value: 2},  
                                                        {name: '3월', value: 3},  
                                                        {name: '4월', value: 4},  
                                                        {name: '5월', value: 5},  
                                                        {name: '6월', value: 6},  
                                                        {name: '7월', value: 7},  
                                                        {name: '8월', value: 8},  
                                                        {name: '9월', value: 9},  
                                                        {name: '10월', value: 10},  
                                                        {name: '11월', value: 11},  
                                                        {name: '12월', value: 12},  
                                                    ],  
                                                    onKeyPress: function (e) {  
  
                                                        if (e.which == Lia.KeyCode.ENTER) {  
                                                            page.searchButton.trigger('click');  
                                                        }  
                                                    }  
                                                })  
                                            });  
                                        }  
                                        break;  
                                    case 'a18':  
                                    case 'a19':  
                                        page.isShowMonth=true;  
                                        page.isShowEquip=true;  
                                        page.isShowSemester=false;  
  
                                        // 항목 선택에 따라 연도에 추가되어야 할 요소: 월  
                                        if(page.isShowMonth){  
                                            // 테이블 검색요소 추가 : 학기  
                                            searchDetailTable.appendRow({})  
                                            searchDetailTable.appendKeyColumn({  
                                                addClass: 'key_column_month',  
                                                content: '월',  
                                            });  
                                            searchDetailTable.appendValueColumn({  
                                                content: page.monthComboBox = new Triton.ComboBox({  
                                                    theme: Triton.ComboBox.Theme.Full,  
                                                    addClass: 'value_column_month',  
                                                    form: {name: 'month'},  
                                                    css : {'width':'150px'},  
                                                    optionList: [  
                                                        {name: '월 선택', value: ''},  
                                                        {name: '1월', value: 1},  
                                                        {name: '2월', value: 2},  
                                                        {name: '3월', value: 3},  
                                                        {name: '4월', value: 4},  
                                                        {name: '5월', value: 5},  
                                                        {name: '6월', value: 6},  
                                                        {name: '7월', value: 7},  
                                                        {name: '8월', value: 8},  
                                                        {name: '9월', value: 9},  
                                                        {name: '10월', value: 10},  
                                                        {name: '11월', value: 11},  
                                                        {name: '12월', value: 12},  
                                                    ],  
                                                    onKeyPress: function (e) {  
  
                                                        if (e.which == Lia.KeyCode.ENTER) {  
                                                            page.searchButton.trigger('click');  
                                                        }  
                                                    }  
                                                })  
                                            });  
                                        }  
  
                                        // 항목 선택에 따라 연도에 추가되어야 할 요소: 장비명  
                                        if(page.isShowEquip){  
                                            searchDetailTable.appendRow({});  
                                            searchDetailTable.appendKeyColumn({  
                                                addClass: 'key_column_equip',  
                                                content: '장비명',  
                                            });  
                                            searchDetailTable.appendValueColumn({  
                                                content: page.equipComboBox = new Triton.ComboBox({  
                                                    theme: Triton.ComboBox.Theme.Full,  
                                                    addClass: 'value_column_equip',  
                                                    form: {name: 'equip'},  
                                                    optionList: [  
                                                        {name: '장비명 선택', value: ''},  
                                                        {name: 'PM-09', value: 'PM-09'},  
                                                        {name: 'PM-10', value: 'PM-10'},  
                                                    ],  
                                                    onKeyPress: function (e) {  
                                                        if (e.which == Lia.KeyCode.ENTER) {  
                                                            page.searchButton.trigger('click');  
                                                        }  
                                                    }  
                                                })  
                                            });  
                                        }  
                                        break;  
                                    default:  
                                        page.isShowMonth=false;  
                                        page.isShowSemester=false;  
                                        page.isShowEquip=false;  
                                        break;  
                                }  
                            },0)  
                        })  
  
                    }  
                })  
            });  
  
            // 테이블 검색요소 추가 : 연도  
            searchDetailTable.appendKeyColumn({  
                content: '연도'  
            });  
            searchDetailTable.appendValueColumn({  
                content: page.yearComboBox = new Triton.ComboBox({  
                    theme: Triton.ComboBox.Theme.Full,  
                    css: {'width': '150px'},  
                    form: {name: 'year'},  
                    optionList: [  
                        {                            name: '2022년',  
                            value: 2022  
                        },  
                        {  
                            name: '2023년',  
                            value: 2023  
                        },  
                        {  
                            name: '2024년',  
                            value: 2024  
                        },  
                        {  
                            name: '2025년',  
                            value: 2025  
                        },  
                        {  
                            name: '2026년',  
                            value: 2026  
                        },  
                        {  
                            name: '2027년',  
                            value: 2027  
                        }  
                    ],  
                    onSelected: function (val) {  
                    }                })  
            });  
  
  
            // 기본값 세팅  
            page.searchItemType.setValue('a1');  
            page.yearComboBox.setValue(2022);  
  
  
        },  
        onRelease: function (j) {  
        }  
    };  
})();
```


- IR 프로젝트 통계분석 - 이런식으로 하면됨
```javascript
let map = {};  
// map['yearList'] = yearList;  
// map['departmentList'] = departmentList.join(",");  
// map['collegeList'] = collegeList.join(",");  
map['param'] = JSON.stringify({  
    "yearList": [2022,2023],  
    "isEvaluated": 1,  
    "promotionStatus": 1,  
    "performanceItemCount": 1,  
    "collegeAndDepartmentList": [  
        {            //   "college": "인문사회대학", //현재 데이터가 단과대 데이터로 들어가있지 않아서 추가 시 조회결과없음.  
            "department": "사회복지학과"  
        }  
    ]})



// 예시데이터
const data = {  
    "yearList": [2022,2023],  
    "isEvaluated": 1,  
    "promotionStatus": 1,  
    "performanceItemCount": 1,  
    "collegeAndDepartmentList": [  
        {            //   "college": "인문사회대학", //현재 데이터가 단과대 데이터로 들어가있지 않아서 추가 시 조회결과없음.  
            "department": "사회복지학과"  
        }  
    ]}
```