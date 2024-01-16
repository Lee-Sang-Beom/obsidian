
#### 1. Requester.awb

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
        
	        // request: HTTP 요청 시 사용한 객체 정보로, 요청 API주소나 parameterMap, method 등이 있다.
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


#### 2. Requester.owb

- `Requester.awb`와 다르게, 새 창을 열면서 API 요청을 수행하는 방법을 사용한다.
	- `window.open()`메소드로 URL 경로와,  `parameter object`를 전달하여, `URL QueryString`를 조합하는 방식을 사용한다.

- 대표적으로 버튼형식의 **첨부파일**을 클릭하였을 때, 커스텀된 `URL QueryString`이 조합되어 API 요청이 완료되자마자 파일이 다운로드되도록 코드를 작성해야 할 때 사용할 수 있다.
	- 아래 **예제**가 그 내용이다.

```javascript
listTable.appendColumn({  
    content: Array.isNotEmpty(Lia.p(item, "attachment_list"))  
        ? new Triton.FlatButton({  
            theme: Triton.FlatButton.Theme.ListNormal,  
            content: "첨부파일",  
  
            // e.data에 item property추가 및 item 데이터 기록  
            item: item,  
            onClick: function (e) {  
                e.stopPropagation();  
                e.preventDefault();  
  
                // e.data: FlatButton을 생성하면서 전달한 option
                // e.data.item: option들 중 item (클릭으로 선택된 데이터를 의미)  
                // table "row"에 해당하는 trition instance에서 해당 데이터를 꺼내려면, Lia.p(triton.getOptions(), 'item') 사용  
  
                var item = e.data.item;  
                var attachmentList = Lia.p(item, "attachment_list");  
                for (var idx in attachmentList) {  
                    var attachmentListItem = Lia.p(attachmentList, idx);  
  
                    // 새 창을 열고, window객체의 open의 argument로 파일을 다운받기 위한 API 경로를 전달하는 방식으로 동작한다.  
  
                    // 두번째 인자로 전달하는 object는 property마다 URL의 QueryString에 추가된다.  
                    Requester.owb(  
                        // /api/file/get  
                        ApiUrl.File.GET,  
                        {  
                            // 예시: /api/file/get?path=temporary/202401//388eb3d6-5707-40d9-99e4-b5b1ccb8bc88.png  
                            path: Lia.p(attachmentListItem, "url"),  
  
                            // 예시: /api/file/get/path=temporary/202401//388eb3d6-5707-40d9-99e4-b5b1ccb8bc88.png&destFilename=board_sample.png  
                            destFilename: Lia.p(  
                                attachmentListItem,  
                                "original_filename"  
                            ),  
                        },  
                        function (status, data, request) {  
                            if (status != Requester.Status.SUCCESS) {  
                            }                        }  
                    );  
                }  
            },  
        })  
        : "-",  
});
```

#### 3. api.js

- 웹사이트, 메시지, 엑셀, 유저, 파일 등 다양한 API 경로를 정의해 둔 파일로, 대분류 한정 CRUD를 정리해둔 느낌이다.

```javascript
  
var AdminApiUrl;  
var ApiUrl = AdminApiUrl = {  
  
    Board: {  
  
        ADD_BOARD: '/api/board/addBoard',  
        ADD_BOARD_CONTENT: '/api/board/addBoardContent',  
        ADD_BOARD_CONTENT_CATEGORY: '/api/board/addBoardContentCategory',  
        ADD_GALLERY_BOARD_CONTENTS: '/api/board/addGalleryBoardContents',  
        DELETE_BOARD_CONTENT: '/api/board/deleteBoardContent',  
        DELETE_BOARD_CONTENT_CATEGORY: '/api/board/deleteBoardContentCategory',  
        DELETE_BOARD_CONTENT_PERMANENTLY: '/api/board/deleteBoardContentPermanently',  
        DOWN_VOTE: '/api/board/downvote',  
        EDIT_BOARD_CONTENT: '/api/board/editBoardContent',  
        EDIT_BOARD_CONTENT_CATEGORY: '/api/board/editBoardContentCategory',  
        GET_BOARD_CONTENT: '/api/board/getBoardContent',  
        GET_BOARD_CONTENT_CATEGORY: '/api/board/getBoardContentCategory',  
        GET_BOARD_CONTENT_CATEGORY_LIST: '/api/board/getBoardContentCategoryList',  
        GET_BOARD_LIST: '/api/board/getBoardList',  
        GET_BOARD: '/api/board/getBoard',  
        RECOVER_BOARD_CONTENT: '/api/board/recoverBoardContent',  
        SET_BOARD_CONTENT_AVAILABILITY: '/api/board/setBoardContentAvailability',  
        SET_BOARD_CONTENT_PRIVATE: '/api/board/setBoardContentPrivate',  
        SET_BOARD_CONTENT_STATUS: '/api/board/setBoardContentStatus',  
        SET_BOARD_CONTENTS_AVAILABILITY: '/api/board/setBoardContentsAvailability',  
        CHECK_BOARD_OWNER_PASSWORD: '/api/board/checkOwnerPassword',  
        UP_VOTE: '/api/board/upvote',  
        SET_BOARD_CONTENT_TARGET_USERS: '/api/board/setBoardContentTargetUsers',  
        MOVE_BOARD_CONTENT: '/api/board/moveBoardContent',  
        DELETE_BOARD_CONTENT_FOR_BOARD_PERMANENTLY: '/api/board/deleteBoardContentForBoardPermanently',  
        // SET_BOARD_CONTENT_REGISTERED_DATE: '/api/board/setBoardContentRegisteredDate',  
  
        GET_BOARD_CONTENT_SUMMARY_LIST: '/api/board/getBoardContentSummaryList',  
        SEARCH_BOARD_CONTENT_SUMMARY_LIST: '/api/board/searchBoardContentSummaryList',  
  
        SET_BOARD_CONTENT_DISPLAY_ORDER: '/api/board/setBoardContentDisplayOrder'  
    },  
  
    File: {  
        CK_EDITOR_UPLOAD: '/api/file/ckEditorUpload',  
        DELETE: '/api/file/delete',  
        GET: '/api/file/get',  
        UPLOAD: '/api/file/upload'  
    },  
  
    Video: {  
        GET_VIMEO_THUMBNAIL: '/api/video/getVimeoThumbnail'  
    },  
  
    User: {  
        ADD_GROUP: '/api/user/addGroup',  
        DELETE_GROUP: '/api/user/deleteGroup',  
        CHANGE_USER_PASSWORD: '/api/user/changeUserPassword',  
        EDIT_GROUP: '/api/user/editGroup',  
        EDIT_USER: '/api/user/editUser',  
        EDIT_USER_PROFILE: '/api/user/editUserProfile',  
        ELEVATE_TEMPORARY_USER: '/api/user/elevateTemporaryUser',  
        FIND_USER_ID: '/api/user/findUserId',  
        GET_GROUP: '/api/user/getGroup',  
        GET_GROUP_SUMMARY_LIST: '/api/user/getGroupSummaryList',  
        GET_USER: '/api/user/getUser',  
        GET_USER_PROFILE: '/api/user/getUserProfile',  
        GET_USER_SUMMARY_LIST: '/api/user/getUserSummaryList',  
        EXPORT_USER_SUMMARY_LIST: '/api/user/exportUserSummaryList',  
        LOGIN: '/api/user/login',  
        LOGIN_WITH_THIRD_PARTY_USER_ACCOUNT: '/api/user/loginWithThirdPartyUserAccount',  
        LOGOUT: '/api/user/logout',  
        REGISTER_USER: '/api/user/registerUser',  
        RESET_USER_PASSWORD: '/api/user/resetUserPassword',  
        SIGN_UP: '/api/user/signUp',  
        VALIDATE_ID: '/api/user/validateId',  
        IMPORT_USERS: '/api/user/importUsers',  
        GET_USER_EMPLOYMENT_STATUS: '/api/user/getUserEmploymentStatus',  
        EDIT_USER_ADDITIONAL_INFO: '/api/user/editUserAdditionalInfo',  
        EXPORT_USERS: '/api/user/exportUsers',  
        GET_USER_EXCEL_IMPORT_TEMPLATE: '/api/user/getUserExcelImportTemplate',  
  
        GET_USER_STATUS_STATISTICS: '/api/user/getUserStatusStatistics',  
        GET_USER_EVENT_STATISTICS: '/api/user/getUserEventStatistics',  
  
        EXPORT_USER_STATUS_STATISTICS: '/api/user/exportUserStatusStatistics',  
        EXPORT_USER_EVENT_STATISTICS: '/api/user/exportUserEventStatistics',  
  
        EDIT_USER_PROPERTIES: '/api/user/editUserProperties'  
    },  
  
    Message: {  
        GET_SENT_MESSAGE_SUMMARY_LIST: '/api/message/getSentMessageSummaryList',  
        GET_COURSE_CONTENT_EDITOR_CONTACT_LIST: '/api/message/getCourseContentEditorContactList',  
        GET_COURSE_ADMINISTRATOR_CONTACT_LIST: '/api/message/getCourseAdministratorContactList',  
        SEND_MESSAGES: '/api/message/sendMessages',  
        GET_SENT_MESSAGE: '/api/message/getSentMessage'  
    },  
  
    Website: {  
        ADD_BANNER: '/api/website/addBanner',  
        ADD_INFORMATION: '/api/website/addInformation',  
        DELETE_BANNER: '/api/website/deleteBanner',  
        DELETE_BANNER_PERMANENTLY: '/api/website/deleteBannerPermanently',  
        DELETE_INFORMATION: '/api/website/deleteInformation',  
        DELETE_INFORMATION_PERMANENTLY: '/api/website/deleteInformationPermanently',  
        EDIT_BANNER: '/api/website/editBanner',  
        EDIT_INFORMATION: '/api/website/editInformation',  
        GET_AVAILABLE_BANNER_LIST: '/api/website/getAvailableBannerList',  
        GET_AVAILABLE_INFORMATION: '/api/website/getAvailableInformation',  
        GET_BANNER: '/api/website/getBanner',  
        GET_BANNER_SUMMARY_LIST: '/api/website/getBannerSummaryList',  
        GET_INFORMATION: '/api/website/getInformation',  
        GET_INFORMATION_SUMMARY_LIST: '/api/website/getInformationSummaryList',  
        SET_BANNER_AVAILABILITY: '/api/website/setBannerAvailability',  
        SET_BANNER_DISPLAY_ORDER: '/api/website/setBannerDisplayOrder',  
        RECOVER_BANNER: '/api/website/recoverBanner',  
  
        //Menu  
        ADD_MENU: '/api/website/addMenu',  
        DELETE_MENU: '/api/website/deleteMenu',  
        DELETE_MENU_PERMANENTLY: '/api/website/deleteMenuPermanently',  
        RECOVER_MENU: '/api/website/recoverMenu',  
        EDIT_MENU: '/api/website/editMenu',  
        GET_MENU: '/api/website/getMenu',  
        GET_MENU_LIST: '/api/website/getMenuList',  
        SET_MENU_AVAILABILITY: '/api/website/setMenuAvailability',  
        SET_MENU_DISPLAY_ORDER: '/api/website/setMenuDisplayOrder',  
  
        GET_MENUS_AND_USERS_EXCEL_IMPORT_TEMPLATE: '/api/website/getMenuUserExcelImportTemplate'  
    },  
  
    System: {  
        EDIT_SYSTEM_VARIABLES: '/api/system/editSystemVariables',  
        GET_SYSTEM_EVENT: '/api/system/getSystemEvent',  
        GET_SYSTEM_EVENT_SUMMARY_LIST: '/api/system/getSystemEventSummaryList',  
        GET_SYSTEM_VARIABLE_LIST: '/api/system/getSystemVariableList'  
    },  
  
    Excel: {  
  
        GET_DOCUMENT_SUMMARY_LIST: '/api/excel/getDocumentSummaryList',  
        GET_EXCEL_IMPORT_RESULT: '/api/excel/getExcelImportResult'  
    },  
  
    Document : {  
        ADD_TERMS_OF_SERVICE: '/api/document/addTermsOfService',  
        EDIT_TERMS_OF_SERVICE: '/api/document/editTermsOfService',  
        GET_TERMS_OF_SERVICE: '/api/document/getTermsOfService',  
        GET_TERMS_OF_SERVICE_SUMMARY_LIST: '/api/document/getTermsOfServiceSummaryList',  
        SET_TERMS_OF_SERVICE_AVAILABILITY: '/api/document/setTermsOfServiceAvailability',  
        DELETE_TERMS_OF_SERVICE: '/api/document/deleteTermsOfService',  
        DELETE_TERMS_OF_SERVICE_PERMANENTLY: '/api/document/deleteTermsOfServicePermanently',  
        RECOVER_TERMS_OF_SERVICE: '/api/document/recoverTermsOfService',  
        REGISTER_USER_TERMS_OF_SERVICE: '/api/document/registerUserTermsOfService',  
  
        GET_CURRENT_SIGN_UP_TERMS_OF_SERVICE: '/api/document/getCurrentSignUpTermsOfService',  
    },  
  
    Survey: {  
  
        ADD_SURVEY: '/api/survey/addSurvey',  
        CANCEL_SURVEY_REQUEST: '/api/survey/cancelSurveyRequest',  
        CHECK_SURVEY_REQUIREMENT: '/api/survey/checkSurveyRequirement',  
        DELETE_SURVEY: '/api/survey/deleteSurvey',  
        DELETE_SURVEY_PERMANENTLY: '/api/survey/deleteSurveyPermanently',  
        EDIT_SURVEY: '/api/survey/editSurvey',  
        GET_SURVEY: '/api/survey/getSurvey',  
        CHECK_SURVEY_RESULT: '/api/survey/checkSurveyResult',  
        EXPORT_SURVEY_RESULT: '/api/survey/exportSurveyResult',  
        GET_SURVEY_REQUEST: '/api/survey/getSurveyRequest',  
        CHECK_SURVEY_REQUEST_RESULT: '/api/survey/checkSurveyRequestResult',  
        EXPORT_SURVEY_REQUEST_RESULT: '/api/survey/exportSurveyRequestResult',  
        GET_SURVEY_SUMMARY_LIST: '/api/survey/getSurveySummaryList',  
        GET_SURVEY_REQUEST_SUMMARY_LIST: '/api/survey/getSurveyRequestSummaryList',  
        RECOVER_SURVEY: '/api/survey/recoverSurvey',  
        SUBMIT_SURVEY: '/api/survey/submitSurvey',  
        SEND_SURVEY_REQUEST: '/api/survey/sendSurveyRequest',  
        TAKE_SURVEY: '/api/survey/takeSurvey',  
        GET_SURVEY_PARTICIPANT_SUMMARY_LIST: '/api/survey/getSurveyParticipantSummaryList',  
        GET_AVAILABLE_SURVEY_REQUEST_SUMMARY_LIST: '/api/survey/getAvailableSurveyRequestSummaryList'  
    },  
  
    Cdn: {  
  
        PURGE: '/api/cdn/purge'  
    },  
  
    Rest: {  
        ADD_API_KEY: '/api/rest/addApiKey',  
        DELETE_API_KEY: '/api/rest/deleteApiKey',  
        EDIT_API_KEY: '/api/rest/editApiKey',  
        GET_API_KEY: '/api/rest/getApiKey',  
        GET_API_KEY_SUMMARY_LIST: '/api/rest/getApiKeySummaryList',  
        SET_API_KEY_AVAILABILITY: '/api/rest/setApiKeyAvailability'  
    },  
  
    Certificate: {  
  
        GET_CERTIFICATE: '/api/certificate/getCertificate'  
    },  
  
    Department: {  
  
        GET_DEPARTMENT_SUMMARY_LIST: '/api/department/getDepartmentSummaryList',  
        GET_DEPARTMENT: '/api/department/getDepartment',  
        ADD_DEPARTMENT: '/api/department/addDepartment',  
        EDIT_DEPARTMENT: '/api/department/editDepartment',  
        DELETE_DEPARTMENT: '/api/department/deleteDepartment',  
  
        //학과 관리자 배정  
        REGISTER_DEPARTMENT_ADMINISTRATOR: '/api/department/registerDepartmentAdministrator',  
        UNREGISTER_DEPARTMENT_ADMINISTRATOR: '/api/department/unregisterDepartmentAdministrator',  
        GET_DEPARTMENT_ADMINISTRATOR_SUMMARY_LIST: '/api/department/getDepartmentAdministratorSummaryList'  
  
    },  
  
  
    Application: {  
  
        //상담 양식 관련  
        ADD_APPLICATION_FORM: '/api/application/addApplicationForm',  
        EDIT_APPLICATION_FORM: '/api/application/editApplicationForm',  
        DELETE_APPLICATION_FORM: '/api/application/deleteApplicationForm',  
        DELETE_APPLICATION_FROM_PERMANENTLY: '/api/application/deleteApplicationFormPermanently',  
        DELETE_USER_APPLICATION_FOR_APPLICATION: '/api/application/deleteUserApplicationForApplication',  
        RECOVER_APPLICATION_FORM: '/api/application/recoverApplicationForm',  
        GET_APPLICATION_FORM_SUMMARY_LIST: '/api/application/getApplicationFormSummaryList',  
        GET_APPLICATION_FORM: '/api/application/getApplicationForm',  
        SET_APPLICATION_FROM_AVAILABILITY: '/api/application/setApplicationFormAvailability',  
  
        //상담 관련  
        ADD_APPLICATION: '/api/application/addApplication',  
        EDIT_APPLICATION: '/api/application/editApplication',  
        DELETE_APPLICATION: '/api/application/deleteApplication',  
        DELETE_APPLICATION_PERMANENTLY: '/api/application/deleteApplicationPermanently',  
        RECOVER_APPLICATION: '/api/application/recoverApplication',  
        GET_APPLICATION: '/api/application/getApplication',  
        GET_APPLICATION_SUMMARY_LIST: '/api/application/getApplicationSummaryList',  
        SET_APPLICATION_AVAILABILITY: '/api/application/setApplicationAvailability',  
  
        //상담 신청 관련  
        WRITE_USER_APPLICATION: '/api/application/writeUserApplication',  
        EDIT_USER_APPLICATION: '/api/application/editUserApplication',  
        DELETE_USER_APPLICATION: '/api/application/deleteUserApplication',  
        CHANGE_USER_APPLICATION_STATUS: '/api/application/changeUserApplicationStatus',  
        DELETE_USER_APPLICATION_PERMANENTLY: '/api/application/deleteUserApplicationPermanently',  
        RECOVER_USER_APPLICATION: '/api/application/recoverUserApplication',  
        COPY_USER_APPLICATION: '/api/application/copyUserApplication',  
        GET_USER_APPLICATION: '/api/application/getUserApplication',  
        GET_USER_APPLICATION_SUMMARY_LIST: '/api/application/getUserApplicationSummaryList',  
        EXPORT_USER_APPLICATION_SUMMARY_LIST: '/api/application/exportUserApplicationSummaryList',  
        GET_USER_APPLICATION_SUMMARY_LIST_BY_USER_INPUT: '/api/application/getUserApplicationSummaryListByUserInput',  
  
        //비밀번호 관련  
        CHECK_USER_APPLICATION_PASSWORD: '/api/application/checkUserApplicationPassword'  
    },  
  
    Calendar: {  
  
        GET_SCHEDULE_PERMISSION: '/api/calendar/getSchedulePermission',  
        GET_SCHEDULE_SUMMARY_LIST: '/api/calendar/getScheduleSummaryList',  
        ADD_SCHEDULE: '/api/calendar/addSchedule',  
        DELETE_SCHEDULE: '/api/calendar/deleteSchedule',  
        EDIT_SCHEDULE: '/api/calendar/editSchedule',  
        EDIT_SCHEDULE_STATUS: '/api/calendar/editScheduleStatus',  
        GET_SCHEDULE: '/api/calendar/getSchedule'  
    },  
  
    Task: {  
  
        GET_ASYNC_TASK: '/api/task/getAsyncTask',  
        GET_ASYNC_TASK_RESULT: '/api/task/getAsyncTaskResult'  
  
    }  
};
```