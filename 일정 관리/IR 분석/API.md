
#### awb

- 대부분의 API 요청에는 `Requester`의 `awb` 메소드를 사용한다.
- 현재 페이지에서의 ajax 요청을 수행하며, 빈 값이 아닌 `parameter`만을 골라내어 새로운 `parameterMap`을 생성하고, 해당 `parameterMap`으로 ajax 요청을 수행한다.
	- **url (문자열)**: HTTP 요청을 보낼 대상 URL
	- **parameterMap (객체)**: HTTP 요청에 포함될 파라미터들을 담고 있는 객체로, 이 객체의 속성과 값은 실제 HTTP 요청에서의 `parameter`의 이름과 값에 해당한다.
	- **onResponse (함수)**: HTTP 요청에 대한 응답이 도착했을 때 호출될 **콜백 함수**
	- **object**: 이벤트 콜백 함수에서 참조할 객체 (어떤 객체에 속한 함수를 이벤트 콜백으로 사용할 때 필요)
	- **noExecuteOnResponseWhenError**: 에러 발생 시 응답 콜백 함수를 실행하지 않을지 여부를 나타내는 booelan 값
	- **q**: 동일한 이름의 파라미터가 여러 개인 경우, 해당 파라미터의 값을 배열로 받는다.
	- **xhrFields**: XMLHttpRequest 객체의 필드를 설정하는 데 사용된다.

```javascript
	   /**
         * @awb : API 요청
         *  - ProjectApiUrl.Project.GET_AUTONOMY_PERFORMANCE_INDICATOR_LIST: 요청경로
         *  - {startYear: ..., endYear: ...} : HTTP 요청에 포함될 객체
         *  - function (status, data, request) {} : 응답 시 수행할 콜백함수
         *    > 여기서, request는 보통 Pager에서 많이 쓴다.
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
```