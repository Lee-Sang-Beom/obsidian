
#### Triton
 - **페이지 초기화**`(onInit)`가 발생할 때나, 특정 이벤트성의 조작으로 **동적으로 페이지 구성**을 진행해야 할 때, 페이지 UI를 형성하기 위한 본 프로젝트 전용 라이브러리이다.
 
 - `new` 생성자로 호출 시, `page` 정보 및 `optional attr값`을 포함하는 Triton 컴포넌트들이 생성되며, `page`에 `append`되는 과정에서 구조화되며 **페이지의 UI가 형성**된다.


#### Triton 사용 예시

```javascript
(function () {  
    return {      
      resultView: true,
  
      onInit: function (j) {
        // page 정보불러오기
        var page = this;
  
        // Triton.Container (div.triton_container.triton_content)
        // 페이지 구성 부분의 최고 부모영역 (여러 section을 감싼다)
        var container = new Triton.Container({
            // j == page.jPage
            appendTo: j
        });
  
        // section (div.triton_section.triton_content)
        // 각종 triton component를 감싸는 "영역"
        var section = new Triton.Section({
            appendTo: container
        });
  
        // page 내 메뉴명
        new Triton.Title({
            appendTo: section,
            content: PageConstructor.getCurrentMenuName(),
        });  

        page.tab = new Triton.Tab({
            appendTo: container,
            theme : Triton.Tab.Theme.Normal, // Triton.Tab에서 정의한 Theme
  
            // 기본동작을 막고, 컨텍스트 메뉴를 생성 및 표시할 것인지
            // 예를들어 선택항목 클릭 시 tab.setTabIndex(index) 호출
            // 이름 바꾸기 항목 클릭 시, 입력팝업 발생
            // 삭제 항목 클릭 시 탭이 삭제
            contextmenuDisabled : true,
            addButtonHidden: true, // 탭을 추가하는 버튼숨김여부
            listButtonHidden: true, // 햄버거바 버튼 숨김여부
            moveButtonHidden: true, // 좌우로 탭을 전환하는 버튼 숨김여부
            readOnly : false, // 탭을 읽기전용으로 구성할 것인가?
            css : {'margin-top' : '20px'},

            // 탭 버튼 추가될 때
            onAdd: function (options) {
            },

            // 탭 선택 되었을 때
            onBind: function (options, index) {
                // option: {name: "탭이름", responsive: booelan 값}
                // index: 탭의 인덱스
  
 
                /**
                 * @cpcpm : Lia.PageManager.goCurrentPageWithCurrentParameterMap;
                 * 해당 메소드는 탭 선택 시, 선택한 탭의 URL로 페이지를 이동시키는 역할을 한다.
                 *
                 * 내부적으로 Lia.PageManager.goPage(undefined, parameterMap, true) 메소드가 호출된다.
                 */
                PageManager.cpcpm({index : index});
  
                /**
                 *
                 * @pcd : 'index'값을 가져온다. 즉, onBind로 탭 클릭 시마다, .pcd함수가 실행되며, 어떤 위치의 탭이 선택되었는지 불러온다.
                 *  
                 * @setTabIndex : 탭 인덱스의 선택 여부에 따라 스타일 변경, 스크롤 위치 조정, 콜백 함수 등을 호출한다. (withoutOnBinadAll이 false인 경우 콜백함수 호출)
                 */
                page.tab.setTabIndex(PageManager.pcd(0, 'index'), true);
  
            }
        });
  
        /**
         * @addTabButton
         *
         * @withoutIndexSetting : 버튼이 추가된 후에 현재의 탭 개수에서 1을 빼서 인덱스를 설정하는 부분이 withoutIndexSetting이 true인 경우에는 실행되지 않음
         *
         * @withoutEditing : 해당 변수가 true로 설정되면, 컨텍스트 메뉴에서 '이름 바꾸기' 항목과 '삭제' 항목이 생성되지 않음. (탭 버튼 우클릭 시, 이름변경 및 삭제옵션이 비활성화)
         */
        page.tab.addTabButton({
            name : "연도별 현황",
        }, true, true);
        page.tab.addTabButton({
            name : "연도별 세부현황",
        }, true, true);
        page.tab.addTabButton({
            name : "전체현황",
        }, true, true);
  
        /**
         * @bodySection : Section 영역을 page에 붙이기 앞서, container에 css 추가 (뺴면 css적으로만 이상해짐)
         *
         * @resultSection : bodySection에 추가 css를 입힘
         * onChange(), detailPage()에서 페이지 출력할 코드에 접근할 떄 해당 page.resultSection에 접근함
         */
  
        page.bodySection = new Triton.Section({
            appendTo: container,
            css: {
                'padding' : '20px 20px 20px 20px',
                'border' : '1px solid ' + TritonTheme.LIST_TABLE_BORDER_COLOR,
                'border-top' : 'none'
            }
        });
  
        page.resultSection = new Triton.Section({ // 현황
            appendTo: page.bodySection,
            css: {
                'overflow' : 'hidden',
            }
        });
  
        page.tab.setTabIndex(PageManager.pcd(0, 'index'), true);
  
 
 
      },
      onChange: function (jPage, jPageContainer, i, parameterMap, beforeParameterMap){
  
        /**
         * @jPage : triton_container triton_content div의 부모 태그
         * @jPageContainer : wrapper 바로아래의 class=`page${n}` 컨테이너 포함 박스
         * @i
         * @parameterMap : 현재 선택된 탭의 정보로, 쿼리스트링 값에서 뽑아오는 것임 {m1: "경로디렉토리/파일명", index: "현재 선택된 탭 인덱스", ...}
         *
         * @beforeParameterMap : 이전에 선택된 탭의 정보 {m1: "경로디렉토리/파일명", index: "이전에 선택된 탭 인덱스", ...}
         *
         * @this : jQuery.Page.Lia.Page로, jPageContainer, jPage 등의 프로퍼티를 가진다.
         */
        var page = this;
  
        // 비동기적으로 함수를 수행하는 코드블록
        Requester.func(function () {
  
            // page.resultSection 객체가 참조하는 요소의 자식요소 모두 제거. (해당 section, container 모두 비움)
            // page의 resultSection은 tab아래의 영역으로, 즉, 메인 컨텐츠영역의 triton_section triton_content 하위 자식요소가 모두 제거된다.
            page.resultSection.empty();
  
            // 현재 페이지 인덱스 가져오기 및, tabIndex 변경에 따른 tab의 여러 기능 및 스타일 설정
            var pageIndex = PageManager.pcd(0, 'index');
            page.tab.setTabIndex(pageIndex, true);
  
 
            // 페이지 인덱스에 따라 하위 페이지에 어떤 detailPage 요소를 추가할 것인지 구분해놓은 메소드 사용
            if (pageIndex == 0) { // 현황 탭 페이지
                page.detailPageOne(page.resultSection, parameterMap);
            } else if (pageIndex == 1) { // 세부현황 탭 페이지
                page.detailPageTwo(page.resultSection, parameterMap);
            } else { // 전체현황 탭 페이지
                page.detailPageThree(page.resultSection, parameterMap);
            }
        });
      },
      detailPageOne: function (appendTo, parameterMap){
        //...
      },
      detailPageTwo: function (appendTo, parameterMap){
        //...
      },
      detailPageThree: function (appendTo, parameterMap){
  
        /**
         * @appendTo : detailPage에 출력할 section, panel을 모두 포함하는 바로 위 부모요소
         * @parameterMap : 현재 선택된 탭의 정보 {m1: "경로디렉토리/파일명", index: "현재 선택된 탭 인덱스"}
         */
        var page = this;
  
        // 상단 검색용도의 Panel을 감싸기위한 Section
        // appendTo로 전달하는 요소 하위에 new Triton.Section 생성자로 추가한 요소들을 붙여나가겠다라고 이해
        var searchSection = new Triton.Section({
            appendTo: appendTo
        });
  
        // 최소 너비 900px의 검색용 Panel영역을 추가
        // searchSection이 <div class="triton_section triton_content">가 <div class=triton_panel>을 감싸게 됨
        // 이것도, 마찬가지로, 인자로넘어온 appendTo 하위에 searchSection이 추가되었고, searchSection 하위에는 이번에 Panel이라는 게 추가되었다고 보면 됨
        var searchPanel = new Triton.Panel({
            appendTo: searchSection,
            contentMinWidth: '900px',
        });
  
        // detailTable 추가 class="triton_detail_column"
        // searchPanel(jContent) 하위에 new Triton.DetailTable(class="triton_detail_table...") 추가
        var searchDetailTable = new Triton.DetailTable({
            appendTo: searchPanel
        });
  
        // table row(행) 추가
        searchDetailTable.appendRow({});
  
        // table에 출력되는 column이되, <th>와 같은 성격의 key가 출력되는 부분
        searchDetailTable.appendKeyColumn({
            content: '시작연도 / 종료연도'
        });
  
        // table에 출력되는 column이되, <td>와 같은 성격의 value가 출력되는 부분
        // appendValueColumn 뒤에 appendItem으로 추가되는 요소들은 triton_detail_table_column_value class 태그요소 하위(<td>와 같음)에 추가된다.
        searchDetailTable.appendValueColumn({
            content: page.startYearComboBox = new Triton.ComboBox({
            // 값 접근시, page.startYearComboBox로 접근 가능
            // 해당 태그(ComboBox) 자체의 존재여무 확인(!==null)이나 접근을 위해서는 PageManager.pc('year')로 접근가능
                theme: Triton.ComboBox.Theme.Normal,
                css: {'width': '150px', 'float': 'left'},
                form: {name: 'start_year'}, // formName을 여기서 설정한다.
  
                // querystring에서, start_year 불러오기
                selectedValue: Lia.p(parameterMap, 'start_year'),
  
                // comboBox DataList
                optionList: [
                    {
                        name: '선택',
                        value: ''
                    },
                    {
                        name: '2022년',
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
                    // 최초 로드 및 comboBox 변경이 이루어지면 실행
                    // ComboBox는 page.startYearComboBox, page.endYearComboBox obj의 getter(getValue()), setter로 접근
                    var startDate = page.startYearComboBox.getValue();
                    var endDate = page.endYearComboBox.getValue();
  
                    if (String.isNotBlank(startDate) && String.isNotBlank(endDate)) {
                        if (endDate < startDate) {
                            PopupManager.alert('안내', '시작연도는 종료연도 이후일 수 없습니다.');
                            page.startYearComboBox.setValue('');
                        }
                    }
                }
            })
        });
  
        // span
       searchDetailTable.appendItem(new Triton.Span({content : '/', css: {'marginTop': '5px','marginLeft': '5px','marginRight': '5px', 'float': 'left'}}))
       
       searchDetailTable.appendItem(page.endYearComboBox = new Triton.ComboBox({
            theme: Triton.ComboBox.Theme.Normal,
            css: {'width': '150px', 'float': 'left'},
            form: {name: 'end_year'},
            selectedValue: Lia.p(parameterMap, 'end_year'),
            optionList: [
                {
                    name: '선택',
                    value: ''
                },
                {
                    name: '2022년',
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
                var startDate = page.startYearComboBox.getValue();
                var endDate = page.endYearComboBox.getValue();
  
                if (String.isNotBlank(startDate) && String.isNotBlank(endDate)) {
  
                    if (endDate < startDate) {
  
                        PopupManager.alert('안내', '종료연도는 시작연도 이전일 수 없습니다.');
  
                        page.endYearComboBox.setValue('');
  
                    }
                }
            }
        }));
  
        /**
         * api 요청 전, 값을 마지막으로 세팅해주는 부분
         * @pc : Lia.PageManger 내부적으로 2회의 메소드를 거친다
         * @getParamter : 맵의 key에 해당하는 value값을 얻고 그 value 값을 반환한다(값이 없으면 빈 문자열 ''을 반환)
         * @getParameterWithChecking : 맵의 key에 해당하는 value값을 얻고 그 value 값을 반환한다(getParameter의 반환값을 이용하며, 빈 문자열 ''이 사전에 반환되었다면, 이를 undefined로 바꿔 반환한다)
         */
        page.startYearComboBox.setValue(PageManager.pc('start_year'));
        page.endYearComboBox.setValue(PageManager.pc('end_year'));
  
        // searchPanel jContent하위에, searchButtonSection이라는 ButtonSection Triton 컴포넌트 추가
        var searchButtonSection = new Triton.ButtonSection({
            appendTo: searchPanel
        });
  
        // ButtonSection 하위에는, trition_section의 style이 float: left인 section과 right인 section 2개가 존재함.
        // appendToLeft 시, float:left인 triton_section 태그 하위에 요소가 추가되고, appendToRight 시, float:right인 곳에 추가된다.
        searchButtonSection.appendToRight(new Triton.FlatButton({
            content: '검색 조건 초기화',
            theme: Triton.FlatButton.Theme.Normal,
            page: page,
            onClick: function (e) {

                /**
                 * @arrayKeyToUnderscore : 객체의 키를 언더스코어 형식으로 변환한 새로운 객체를 반환.
                 * 주어진 객체의 각 키에 대해 "toUnderScore"함수를 사용하여, 언더스코어 형식으로 변환한 새로운 키를 생성하고, 이 새로운 key와 기존 value를 연결한 새로운 객체 반환
                 *
                 * @toUnderscore : 주어진 문자열에서 정규식에 해당하는 대문자를 찾고, 소문자 변경 및 언더하이프로 변환하여 반환한다.
                 *
                 * @extractFormData : 전달받은 인자로부터 "triton_form" 요소(jQuery객체)를 찾고, 요소를 for문으로 돌면서 extractFormData를 호출한다.
                 * find: 선택자(page) 기준 모든 하위요소인 .triton_form 검색 (선택된 요소의 하위요소를 찾음)
                 * filter: 선택자(page) 그 자체로 조회된 요소들 중, 특정 조건을 만족하는 하위 요소를 찾는 데 사용 (선택된 요소 그 자체나 조건을 만족하는 요소 탐색)
                 *
                 * @extractFormDataEach : "trition_form" class명을 포함하는 jQuery객체로부터 Triton 폼 요소(인스턴스), 폼 이름 등을 추출한다.
                 * 이후, 폼 요소(인스턴스)로부터 getValue으로 값을 얻고, formData[formNMAE] = value로, 인자로 전달한 formData의 key-value쌍 데이터를 하나씩 추가한다.
                 */
                var item = FormatHelper.arrayKeyToUnderscore(Triton.extractFormData(page.get()));
  
                // 사전에 사용자가 입력했던 내용이 있으니, 이미 있던 검색 조건을 초기화
                for (var key in item) {
                    item[key] = '';
                }
                // page.startYearComboBox.setValue('');
                // page.endYearComboBox.setValue('');
  
                // page는 처음으로 고정해야함
                item['page'] = 1;
                // 폼 요소에서 선택한 값(검색조건)을 기반으로, 페이지 이동 수행
                PageManager.cpcpm(item);
            }
        }));
        searchButtonSection.appendToRight(new Triton.FlatButton({
            content: '검색',
            theme: Triton.FlatButton.Theme.Normal,
            page: page,
            onClick: function (e) {
                // e.data.page === page === jQuery.Page.Lia.Page
                // page.get() === jPage (jQuery.Page.Lia.Page 내의 jPage property)
                var page = e.data.page;
                var item = FormatHelper.arrayKeyToUnderscore(Triton.extractFormData(page.get()));
                item['page'] = 1;
  
                // item : {start_year: '선택한값', end_year: '선택한값', page: 1}
                PageManager.cpcpm(item);
            }
        }));
  
        // appendTo jContent 하위에 panel 추가
        var panel = new Triton.Panel({
            appendTo: appendTo,
            contentMinWidth: '2400px',
            css: {'margin-top': '20px', 'margin-bottom' : '10px'}
        });
  
        // panel jContent 하위에 detailTable 추가
        var detailTable = new Triton.DetailTable({
            appendTo: panel
        });
  
        // 테이블 row(num: 1) 추가
        detailTable.appendRow({});
        // 테이블 row(num: 1)에 해당하는 <th> 생성(appendKeyColumn) <td>는 (appendValueColumn)
        detailTable.appendKeyColumn({content: '성과지표', css: {'width' : '3%'},attr:{rowSpan:2}});
        detailTable.appendKeyColumn({content: '지표명', css: {'width' : '5%'},attr:{rowSpan:2}});
        detailTable.appendKeyColumn({content: '관점', css: {'width' : '3%'},attr:{rowSpan:2}});
        detailTable.appendKeyColumn({content: '요소', css: {'width' : '7%'},attr:{rowSpan:2}});
        detailTable.appendKeyColumn({content: '성과지표', css: {'width' : '15%'},attr:{rowSpan:2}});
        detailTable.appendKeyColumn({content: '2022년', css: {'width' : '11%'}, attr:{colSpan:3}});
        detailTable.appendKeyColumn({content: '2023년', css: {'width' : '11%'}, attr:{colSpan:3}});
        detailTable.appendKeyColumn({content: '2024년', css: {'width' : '11%'}, attr:{colSpan:3}});
        detailTable.appendKeyColumn({content: '2025년', css: {'width' : '11%'}, attr:{colSpan:3}});
        detailTable.appendKeyColumn({content: '2026년', css: {'width' : '11%'}, attr:{colSpan:3}});
        detailTable.appendKeyColumn({content: '2027년', css: {'width' : '11%'}, attr:{colSpan:3}});
  
        // 테이블 row(num: 2) 추가
        detailTable.appendRow({});
  
        // 테이블 row(num: 2)에 해당하는 <th> 생성(appendKeyColumn) <td>는 (appendValueColumn)
        detailTable.appendKeyColumn({content: '목표값'});
        detailTable.appendKeyColumn({content: '달성값'});
        detailTable.appendKeyColumn({content: '달성률'});
        detailTable.appendKeyColumn({content: '목표값'});
        detailTable.appendKeyColumn({content: '달성값'});
        detailTable.appendKeyColumn({content: '달성률'});
  
        detailTable.appendKeyColumn({content: '목표값'});
        detailTable.appendKeyColumn({content: '달성값'});
        detailTable.appendKeyColumn({content: '달성률'});
  
        detailTable.appendKeyColumn({content: '목표값'});
        detailTable.appendKeyColumn({content: '달성값'});
        detailTable.appendKeyColumn({content: '달성률'});
  
        detailTable.appendKeyColumn({content: '목표값'});
        detailTable.appendKeyColumn({content: '달성값'});
        detailTable.appendKeyColumn({content: '달성률'});
  
        detailTable.appendKeyColumn({content: '목표값'});
        detailTable.appendKeyColumn({content: '달성값'});
        detailTable.appendKeyColumn({content: '달성률'});
  
        // 테이블 row(num: 3) 추가
        detailTable.appendRow({});
  
        // 테이블 row(num: 3)에 해당하는 <th> 생성(appendKeyColumn) <td>는 (appendValueColumn)
        detailTable.appendKeyColumn({content: '3S지수', attr: {'rowspan' : '22'}});
        detailTable.appendKeyColumn({content: '진정성지수', attr: {'rowspan' : '11'}});
        detailTable.appendKeyColumn({content: '대학본부', attr: {'rowspan' : '4'}});
        detailTable.appendKeyColumn({content: '비대학본부관심도</br>(LINC→대학)', attr: {'rowspan' : '2'}});
        detailTable.appendValueColumn({content: 'LINC사업단인력중대학보직비율(%)'});
  
        //2022
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'goals'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'attainment'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'accomplishment_rate'});
  
        //2023
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'goals'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'attainment'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'accomplishment_rate'});
  
        //2024
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'goals'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'attainment'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'accomplishment_rate'});
  
        //2025
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'goals'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'attainment'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'accomplishment_rate'});
  
        // 2026
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'goals'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'attainment'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'accomplishment_rate'});
  
        // 2027
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'goals'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'attainment'});
        detailTable.appendValueColumn({content: '0' + '%', css: {'text-align' : 'center'}, addClass:'accomplishment_rate'});
 

        /**
         * 기타 코드 작성
         */
  
        // 둘 다 null이면, 페이지 첫 진입일 수도 있으니, alert없이 알아서
        if(PageManager.pc('start_year') == null && PageManager.pc('end_year') == null){
            return;
        }
  
        // .pc로 얻은 데이터는 값이 없는경우 undefined임. 잘못 작성된 코드
        if(PageManager.pc('start_year') == null){
  
            // alert 출력
            PopupManager.alert('안내', '시작년도를 선택해주세요.');
            return;
        }
  
        // .pc로 얻은 데이터는 값이 없는경우 undefined임. 잘못 작성된 코드
        if(PageManager.pc('end_year') == null){
            PopupManager.alert('안내', '종료년도를 선택해주세요.');
            return;
        }
  
        /**
         * @awb : API 요청
         */
        Requester.awb(ProjectApiUrl.Project.GET_AUTONOMY_PERFORMANCE_INDICATOR_LIST, {
            // parameterMap
            startYear: page.startYearComboBox.getValue(),
            endYear: page.endYearComboBox.getValue(),
        }, function (status, data, request) {
            // onResponse callback fn
            if (status != Requester.Status.SUCCESS) {
                return;
            }
  
            // data의 body property 출력
            var body = Lia.p(data, 'body');
  
            // data의 body, list property 출력
            var list = Lia.p(data, 'body', 'list');
  
            /**
             * @convertListToListMap : 2번째 인자로 전달한 key이름으로, 새로운 배열객체를 만들어 반환한다.
             * 1번째 인자로 전달한 list 내에, 2번째 인자로 전달한 key이름인 property명이 존재해야하며,
             * 아래 예제에서는 map이라는 배열을 만들고, map["year"]에 list의 구성 value이 반복문을 통해 push되는 과정을 거친다.
             *
             * ex) businessList = {2022: Array(93), 2023: Array(93)}
             */
            var businessList = Lia.convertListToListMap(list, 'year');
  
            for( var i in businessList){
                var businessListItem = businessList[i];
                var businessIndex;
  
                // converListToListMap 메소드를 통해 배열 property명을 year로 구성한 이유임.
                // businessIdx를 year기준으로 나눠, businessIndex 값을 구성함
                switch (i){
                    case '2022': businessIndex = 0 ; break;
                    case '2023': businessIndex = 1 ; break;
                    case '2024': businessIndex = 2 ; break;
                    case '2025': businessIndex = 3 ; break;
                    case '2026': businessIndex = 4 ; break;
                    case '2027': businessIndex = 5 ; break;
                }
  
                for( var i in businessListItem){
                    // 연도별 데이터 하나하나 확인
                    var item = businessListItem[i];
                    if(Lia.p(item, 'elementDepth') != 1){
                        continue
                    }
  
                    /**
                     * @eq : 페이지에서 클래스가 '[class명]'인 모든 요소를 찾은 다음, return된요소 리스트에서 index에 해당하는 요소를 선택한다.
                     * @text : 선택된 요소의 텍스트 내용을 "text"로 설정
                     * @addComman : Formatting Func. number를 매개변수로 받고, 포맷이 적용된 숫자를 반환한다. (첫번째로 주어진 숫자를 1000단위로 쉼표를 삽입하여 formatting하며, 소수점 이하 부분도 포함되어 있으면 해당 부분도 함께 formatting)
                     * @p : 첫번째 매개변수로 전달한 object에서, 2번째 매개변수로 전달한 'key'값에 대응하는 value를 반환한다.
                     */
                    page.find('.goals').eq(businessIndex).text(ProjectFormatHelper.addComman(Lia.p(item, 'target_value')))
                    page.find('.attainment').eq(businessIndex).text(ProjectFormatHelper.addComman(Lia.p(item, 'achieved_value')))
                    page.find('.accomplishment_rate').eq(businessIndex).text(ProjectFormatHelper.addComman(Lia.p(item, 'achieved_rate'))+'%')
  
                    // 연속 출력을 위해 +6씩 추가
                    // 2022, 2023, 2024, 2025, 2026, 2027 총 6개연도 기준 출력이고, businessListItem도 'year'를 기준으로 재구성된 배열임
                    // 연도별 목표/달성값/달성률을 작성했으면, 동일연도의 다음 성과지표 데이터의 목표/달성값/달성률을 작성해야하므로 +6
                    businessIndex += 6;
                }
            }
  
        });
    },
 
    onRelease: function (j) {
    }
 
    },
})();
```



