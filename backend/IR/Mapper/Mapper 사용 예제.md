
#### 1. 자바스크립트 코드에서 함수 호출

- 만약, 자바스크립트 코드에서 다음과 같이 API를 호출했다고 치자.
```js
AjaxPopupManager.show(PopupUrl.EXPORT_DOWNLOAD_PROGRESS, {  
    url: ProjectApiUrl.Excel.GET_CAP_STONE_DESIGN_EXCEL_DOWNLOAD,  
    requestMap: {year: PageManager.pc('year'),collegeName: Lia.pd(null, parameterMap, 'daehak_cd1'), searchOptionList: searchOption != null ? searchOption.get():null}  
});
```

- 그럼, 이제 컨트롤러로 사용자 요청이 들어온다.
```java
@RequestMapping(value = "/getCapStoneDesignExcelDownload", method = RequestMethod.POST)  
@ResponseBody  
public String getCapStoneDesignExcelDownload(HttpServletRequest httpServletRequest,  
                                             @RequestParam(value = "searchOptionList", required = false) String searchOptionListString,  
                                             @RequestParam(value = "year", required = false) Integer year,  
                                             @RequestParam(value = "collegeName", required = false) String collegeName,  
                                             @SessionValue Session session,  
                                             HttpServletResponse httpServletResponse) throws Exception {  
  
    Response response = null;  
  
    List<SearchOption> searchOptionList = SearchOptionUtils.parse(searchOptionListString,  
                                                                  super.getDatabaseType());  
  
    try {AsyncTask asyncTask = this.educationService.getCapStoneDesignExcelDownload(session, httpServletRequest,year, searchOptionList,collegeName);  
  
        response = super.responseFactory.createResponse(Code.SUCCESS,  
                                                        asyncTask.toJsonAware(),  
                                                        httpServletRequest.getLocale());  
  
    } catch (CodedException e) {  
  
        response = super.responseFactory.createResponse(e,  
                                                        httpServletRequest.getLocale());  
    }  
  
    return response.toJSONString();  
  
}
```

- 그리고, 전달받은 인자로 `this.educationService.getCapStoneDesignExcelDownload()` 메소드를 사용하는 것을 볼 수 있다.
```java
try {AsyncTask asyncTask = this.educationService.getCapStoneDesignExcelDownload(session, httpServletRequest,year, searchOptionList,collegeName);  
  
    response = super.responseFactory.createResponse(Code.SUCCESS,  
                                                    asyncTask.toJsonAware(),  
                                                    httpServletRequest.getLocale());  
  
} catch (CodedException e) {  
  
    response = super.responseFactory.createResponse(e,  
                                                    httpServletRequest.getLocale());  
}
```


#### 2. 서비스로 이동

- 서비스에서는 아래와 같은 로직을 호출한다.
	- 컨트롤러는 서비스를 호출하고, 서비스에서는 주요 비즈니스 로직을 처리해준다.
```js
  
@Override  
@AllowNotLoggedIn(false)  
public AsyncTask getCapStoneDesignExcelDownload(Session session, HttpServletRequest httpServletRequest, Integer year,  
                                                List<SearchOption> searchOptionList, String collegeName) throws Exception {  
    final AsyncTask asyncTask = this.taskService.registerAsyncTask(AsyncTask.Type.EXPORT_EXCEL);  
  
    final long asyncTaskId = asyncTask.getId();  
  
    new Thread(new Runnable() {  
  
        @Override  
        public void run() {  
  
            Workbook workbook = null;  
  
            try {  
  
                taskService.updateAsyncTaskStatus(asyncTaskId,  
                        null,  
                        null,  
                        AsyncTask.Status.PROCESSING);  
  
                workbook = doCapStoneDesignExcelDownload(session,  
                        year,  
                        searchOptionList,  
                        collegeName);  
  
                String resultFilePath = taskService.saveAsyncTaskResult(asyncTaskId,  
                        workbook);  
  
                taskService.updateAsyncTaskStatus(asyncTaskId,  
                        null,  
                        resultFilePath,  
                        AsyncTask.Status.COMPLETED);  
  
            } catch (Exception e) {  
  
                e.printStackTrace();  
  
                try {  
  
                    JSONAware properties = null;  
  
                    if (e instanceof CodedException) {  
  
                        CodedException codedException = (CodedException) e;  
                        properties = codedException.toJsonAware();  
                    }  
  
                    taskService.updateAsyncTaskStatus(asyncTaskId,  
                            properties,  
                            null,  
                            AsyncTask.Status.FAILED);  
  
                } catch (Exception e1) {  
  
                    // TODO  
                    e1.printStackTrace();  
                }  
  
            } finally {  
  
                try {  
  
                    if (workbook != null) workbook.close();  
  
                } catch (IOException e) {  
  
                    e.printStackTrace();  
                }  
            }  
        }  
    }).start();  
  
    return asyncTask;  
}
```

- 위의 코드 중 `doCapStoneDesignExcelDownload` 메소드는 아래와 같이 정의되어 있다.
```java
  
    private Workbook doCapStoneDesignExcelDownload(Session session,  
                                                   Integer year,  
                                                   List<SearchOption> searchOptionList,  
                                                   String collegeName) throws Exception {  
  
        Workbook workbook = new HSSFWorkbook();  
  
        String department = null;  
  
        if (session.getUserRole() == User.Role.parseRole(20)) {  
            collegeName = session.getUserName();  
        }  
  
        boolean departmentManager = true;  
  
        if (session.getUserRole() == User.Role.parseRole(21)) {  
  
            if (searchOptionList != null) {  
                for (SearchOption SearchOptionItem : searchOptionList) {  
                    if (SearchOptionItem.getCode() == 2) {  
                        SearchOptionItem.setKeyword(session.getUserName());  
                        departmentManager = false;  
                    }  
                }  
            }  
            if (departmentManager) {  
                searchOptionList = new ArrayList<SearchOption>();  
                SearchOption searchOption = new SearchOption();  
                searchOption.setCode(2);  
                searchOption.setKeyword(session.getUserName());  
                searchOptionList.add(searchOption);  
            }  
        }  
  
        List<CapStu> capForStudentList = this.portalMapper.selectCapForStudent(searchOptionList, year, null, null, collegeName);  
  
  
        String[] columnNames = {"연도", "학기", "단과대학", "학과(부)", "학번", "이름", "학년", "담당교수", "강좌번호", "과목명", "프로젝트명", "기업연계유무", "기업명", "학점"};  
  
        Sheet sheet = workbook.createSheet();  
  
        workbook.setSheetName(0,  
                year + "캡스톤디자인 이수현황");  
  
        // Header  
        int columnCount = columnNames.length;  
        int currentRowIdx = 0;  
        Row headerRow = sheet.createRow(currentRowIdx);  
  
        for (int i = 0; i < columnCount; i++) {  
  
            CellStyle headerCellStyle = workbook.createCellStyle();  
            headerCellStyle.setAlignment(HorizontalAlignment.CENTER);  
            headerCellStyle.setBorderTop(BorderStyle.THIN);  
            headerCellStyle.setBorderBottom(BorderStyle.THIN);  
            headerCellStyle.setBorderLeft(BorderStyle.THIN);  
            headerCellStyle.setBorderRight(BorderStyle.THIN);  
  
            String columnName = columnNames[i];  
  
            Cell headerCell = headerRow.createCell(i);  
            headerCell.setCellStyle(headerCellStyle);  
            headerCell.setCellValue(columnName);  
        }  
  
        currentRowIdx++;  
  
        // Body  
        CellStyle bodyCellStyle = workbook.createCellStyle();  
        bodyCellStyle.setAlignment(HorizontalAlignment.CENTER);  
        bodyCellStyle.setBorderTop(BorderStyle.THIN);  
        bodyCellStyle.setBorderBottom(BorderStyle.THIN);  
        bodyCellStyle.setBorderLeft(BorderStyle.THIN);  
  
//        Integer sheetCount = 1;  
//        Integer sheetCurrentRowIdx = 1;  
  
        for (CapStu capForStudentItem : capForStudentList) {  
//  
//            if(currentRowIdx == 60000 * sheetCount){  
////  
////                for (int i = 0; i < currentRowIdx; i++) {  
////  
////                    sheet.autoSizeColumn(i);  
////                }  
//  
//                sheet = workbook.createSheet();  
//  
//                workbook.setSheetName(sheetCount,  
//                                      year+"캡스톤디자인 이수현황"+sheetCount);  
//  
//                // Header  
//                headerRow = sheet.createRow(0);  
//                for (int i = 0; i < columnCount; i++) {  
//  
//                    CellStyle headerCellStyle = workbook.createCellStyle();  
//                    headerCellStyle.setAlignment(HorizontalAlignment.CENTER);  
//                    headerCellStyle.setBorderTop(BorderStyle.THIN);  
//                    headerCellStyle.setBorderBottom(BorderStyle.THIN);  
//                    headerCellStyle.setBorderLeft(BorderStyle.THIN);  
//                    headerCellStyle.setBorderRight(BorderStyle.THIN);  
//  
//                    String columnName = columnNames[i];  
//  
//                    Cell headerCell = headerRow.createCell(i);  
//                    headerCell.setCellStyle(headerCellStyle);  
//                    headerCell.setCellValue(columnName);  
//                }  
//                sheetCount++;  
//                sheetCurrentRowIdx = 1;  
//            }  
  
            Row bodyRow = sheet.createRow(currentRowIdx);  
  
            for (int i = 0; i < columnCount; i++) {  
  
                Cell bodyCell = bodyRow.createCell(i);  
                bodyCell.setCellStyle(bodyCellStyle);  
  
                switch (i) {  
  
                    case 0: {  
  
                        String item = capForStudentItem.getYy();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 1: {  
  
                        Object item = capForStudentItem.getTerm();  
  
                        if (item != null) {  
                            String itemString = capForStudentItem.getTerm();  
  
                            switch (itemString) {  
                                case "1":  
                                    itemString = "1학기";  
                                    break;  
                                case "2":  
                                    itemString = "2학기";  
                                    break;  
                                case "3":  
                                    itemString = "여름방학";  
                                    break;  
                                case "4":  
                                    itemString = "겨울방학";  
                                    break;  
                            }  
  
                            bodyCell.setCellValue(itemString);  
                        }  
                        break;  
                    }  
  
                    case 2: {  
                        String item = capForStudentItem.getCollegeName();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 3: {  
                        String item = capForStudentItem.getDeptNm();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 4: {  
                        String item = capForStudentItem.getStudId();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 5: {  
                        String item = capForStudentItem.getStudNm();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 6: {  
                        String item = capForStudentItem.getStuGrade();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 7: {  
                        String item = capForStudentItem.getEmpNm();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 8: {  
                        String item = capForStudentItem.getSubjectNo();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 9: {  
                        String item = capForStudentItem.getSubjectNm();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 10: {  
                        String item = capForStudentItem.getProjectNm();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 11: {  
                        String item = capForStudentItem.getProjectTypeCode();  
  
                        if (item != null) {  
  
                            switch (item) {  
                                case "1":  
                                    item = "일반";  
                                    break;  
                                case "2":  
                                    item = "기업연계";  
                                    break;  
                                case "3":  
                                    item = "융합";  
                                    break;  
                                case "4":  
                                    item = "글로벌";  
                                    break;  
                            }  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
                    case 12: {  
                        String item = capForStudentItem.getCompanyNm();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
                    case 13: {  
                        String item = capForStudentItem.getGrade();  
  
                        if (item != null) {  
  
                            bodyCell.setCellValue(item);  
                        }  
                        break;  
                    }  
  
  
                    default: {  
  
                        break;  
                    }  
                }  
            }  
//            currentRowIdx++;  
//            sheetCurrentRowIdx++;  
            currentRowIdx++;  
        }  
  
        for (int i = 0; i < currentRowIdx; i++) {  
            sheet.autoSizeColumn(i);  
        }  
  
        return workbook;  
    }
```

- 굉장히 긴데, 여기서 우리는 아래 코드에 집중할 것이다.
```java
List<CapStu> capForStudentList = this.portalMapper.selectCapForStudent(searchOptionList, year, null, null, collegeName);
```


#### 3. Mapper

```js
List<CapStu> selectCapForStudent(@Param("search_option_list") List<SearchOption> searchOptionList,  
                                 @Param("year") Integer year,  
                                 @Param("page") Integer page,  
                                 @Param("count") Integer count,  
                                 @Param("college_name") String collegeName) throws Exception;
```
- 이 코드는 MyBatis의 매퍼 인터페이스에 정의된 메서드이다.
	- 이 메서드는 주어진 검색 옵션에 따라 학생들의 정보를 검색하여 해당하는 `CapStu` 객체들의 리스트를 반환하는 역할을 수행한다.
	- 이는 데이터베이스에서 쿼리를 실행하여 결과를 가져오는 역할을 한다.


- 연결된 xml 코드로 이동하면 아래와 같은 결과가 있다.
```xml
<select id="selectCapForStudent" resultMap="capResultMap">  
    SELECT yy,  
    term,    college_name,    dept_nm,    stud_id,    stud_nm,    stu_grade,    emp_nm,    subject_no,    subject_nm,    project_nm,    project_type,    project_type_code,    grade,    mark,    company_nm    FROM    (    SELECT    ( CASE WHEN H.yy IS NOT NULL THEN H.yy ELSE A.yy END ) AS yy,    ( CASE WHEN H.term IS NOT NULL THEN H.term ELSE A.term END ) AS term,    ( CASE WHEN H.college_name IS NOT NULL THEN H.college_name ELSE C.college_name END ) AS college_name,    ( CASE WHEN H.dept_nm IS NOT NULL THEN H.dept_nm ELSE C.department_name END ) AS dept_nm,    ( CASE WHEN H.stud_id IS NOT NULL THEN H.stud_id ELSE A.stud_id END ) AS stud_id,    ( CASE WHEN H.stud_nm IS NOT NULL THEN H.stud_nm ELSE A.stud_nm END ) AS stud_nm,    ( CASE WHEN H.stu_grade IS NOT NULL THEN H.stu_grade ELSE F.stu_schgr END ) AS stu_grade,    ( CASE WHEN H.emp_nm IS NOT NULL THEN H.emp_nm ELSE A.emp_nm END ) AS emp_nm,    ( CASE WHEN H.subject_no IS NOT NULL THEN H.subject_no ELSE A.subject_no END ) AS subject_no,    ( CASE WHEN H.subject_nm IS NOT NULL THEN H.subject_nm ELSE A.subject_nm END ) AS subject_nm,    ( CASE WHEN H.project_nm IS NOT NULL THEN H.project_nm ELSE B.project_nm END ) AS project_nm,    ( CASE WHEN H.project_type IS NOT NULL THEN ( CASE    WHEN H.PROJECT_TYPE = 1 THEN '일반'  
    WHEN H.PROJECT_TYPE = 2 THEN '기업연계'  
    WHEN H.PROJECT_TYPE = 3 THEN '융합'  
    WHEN H.PROJECT_TYPE = 4 THEN '글로벌'  
    END    ) ELSE B.project_type END ) AS project_type,    ( CASE WHEN H.project_type IS NOT NULL THEN ( CASE    WHEN H.PROJECT_TYPE = 1 THEN 1    WHEN H.PROJECT_TYPE = 2 THEN 2    WHEN H.PROJECT_TYPE = 3 THEN 3    WHEN H.PROJECT_TYPE = 4 THEN 4    END    ) ELSE ( CASE    WHEN (!(B.PROJECT_TYPE IN ('기업애로사항해결형', '학과연계형(융합형)')) || B.PROJECT_TYPE IS NULL ) THEN 1  
    WHEN B.PROJECT_TYPE = '기업애로사항해결형' THEN 2  
    WHEN B.PROJECT_TYPE = '학과연계형(융합형)' THEN 3  
    END    ) END ) AS project_type_code,    ( CASE WHEN H.grade IS NOT NULL THEN H.grade ELSE A.grade END ) AS grade,    ( CASE WHEN H.mark IS NOT NULL THEN H.mark ELSE A.mark END ) AS mark,    H.company_name AS company_nm    FROM vsquare_subject_score A    LEFT OUTER JOIN tb_edit_cap_stone_design H    ON H.yy = A.yy    AND  H.term = A.term    AND  H.stud_id = A.stud_id    AND  H.subject_no = A.subject_no    inner JOIN tb_previous_participation_department_name E    ON E.department_name = A.dept_nm    AND  A.yy = E.year    inner JOIN tb_participation_department C    ON (C.linc_status = 'O' || C.linc_status = 'o')    AND E.tb_participation_department_id = C.id    inner JOIN tb_cap_stone_design_lecture D    ON  A.subject_no = D.number    AND A.yy = D.year    AND A.term = D.term    LEFT OUTER JOIN tb_cap_stone_design_delete G    ON A.yy = G.year    AND A.term = G.haggi    AND A.stud_id = G.hagbeon    AND A.subject_no = G.subject_id    LEFT OUTER JOIN vsquare_stu_inf F    ON A.stud_id = F.intg_uid    LEFT OUTER JOIN (    SELECT DISTINCT C.YEAR,C.HAKGI,C.SUBJECT_ID, C.PROF_NM, C.PROJECT_NM, A.STU_ID ,A.STU_GRADE, C.PROJECT_TYPE    FROM v_capston_member A    INNER JOIN v_capston_member B    ON B.TEAM_NM = A.TEAM_NM AND B.YEAR = A.YEAR AND B.HAKGI = A.HAKGI    INNER JOIN v_capston_result C    ON C.TEAM_NM = B.TEAM_NM AND C.YEAR = B.YEAR AND C.HAKGI = B.HAKGI    WHERE 1=1    AND B.POSITION_NM = '팀장'  
    ) B    ON B.SUBJECT_ID = A.subject_no    AND A.emp_nm LIKE CONCAT('%',B.PROF_NM,'%')    AND A.stud_id = B.STU_ID    AND A.YY = B.YEAR    AND (    CASE    WHEN A.TERM = 1 THEN '1학기'  
    WHEN A.TERM = 2 THEN '2학기'  
    WHEN A.TERM = 3 THEN '여름방학'  
    WHEN A.TERM = 4 THEN '겨울방학'  
    END    ) = B.HAKGI    WHERE 1 = 1    AND G.year IS NULL    <if test="year != null">  
        AND ( CASE WHEN H.yy IS NOT NULL THEN H.yy ELSE A.yy END ) = #{year}  
    </if>  
    <if test="search_option_list != null">  
        <foreach collection="search_option_list" item="search_option">  
            <if test="search_option.code == 1">  
                AND ( CASE WHEN H.TERM IS NOT NULL THEN H.TERM ELSE A.TERM END ) = (  
                CASE                WHEN #{search_option.keyword} = 10 THEN '1'                WHEN #{search_option.keyword} = 20 THEN '2'                WHEN #{search_option.keyword} = 11 THEN '3'                WHEN #{search_option.keyword} = 21 THEN '4'                END                )            </if>  
            <if test="search_option.code == 2">  
                AND ( CASE WHEN H.DEPT_NM IS NOT NULL THEN H.DEPT_NM ELSE C.department_name  END ) LIKE CONCAT('%', #{search_option.keyword}, '%')  
            </if>  
            <if test="search_option.code == 3">  
                AND ( CASE WHEN H.emp_nm IS NOT NULL THEN H.emp_nm ELSE A.emp_nm END ) LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 4">  
                AND ( CASE WHEN H.grade IS NOT NULL THEN H.grade ELSE A.grade END ) LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 5">  
                AND ( CASE WHEN H.project_type IS NOT NULL THEN ( CASE  
                WHEN H.PROJECT_TYPE = 1 THEN 1                WHEN H.PROJECT_TYPE = 2 THEN 2                WHEN H.PROJECT_TYPE = 3 THEN 3                WHEN H.PROJECT_TYPE = 4 THEN 4                END                ) ELSE ( CASE                WHEN (!(B.PROJECT_TYPE IN ('기업애로사항해결형', '학과연계형(융합형)')) || B.PROJECT_TYPE IS NULL ) THEN 1  
                WHEN B.PROJECT_TYPE = '기업애로사항해결형' THEN 2  
                WHEN B.PROJECT_TYPE = '학과연계형(융합형)' THEN 3  
                END                ) END ) = #{search_option.keyword}            </if>  
            <if test="search_option.code == 6">  
                AND ( CASE WHEN H.subject_nm IS NOT NULL THEN H.subject_nm ELSE A.subject_nm END ) LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 7">  
                AND ( CASE WHEN H.project_nm IS NOT NULL THEN H.project_nm ELSE B.project_nm END ) LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 8">  
                AND H.company_name LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
        </foreach>  
    </if>  
    <if test="college_name != null">  
        AND ( CASE WHEN H.college_name IS NOT NULL THEN H.college_name ELSE C.college_name END ) = #{college_name}  
    </if>  
  
    UNION ALL  
  
    SELECT yy,    term,    college_name,    dept_nm,    stud_id,    stud_nm,    stu_grade,    emp_nm,    subject_no,    subject_nm,    project_nm,    ( CASE    WHEN PROJECT_TYPE = 1 THEN '일반'  
    WHEN PROJECT_TYPE = 2 THEN '기업연계'  
    WHEN PROJECT_TYPE = 3 THEN '융합'  
    WHEN PROJECT_TYPE = 4 THEN '글로벌'  
    END    ) AS project_type,    PROJECT_TYPE AS 'project_type_code',    grade,    mark,    company_name AS company_nm    FROM tb_add_cap_stone_design    WHERE 1 = 1    <if test="year != null">  
        AND yy LIKE CONCAT('%',#{year},'%')  
    </if>  
    <if test="search_option_list != null">  
        <foreach collection="search_option_list" item="search_option">  
            <if test="search_option.code == 1">  
                AND term = (  
                CASE                WHEN #{search_option.keyword} = 10 THEN '1'                WHEN #{search_option.keyword} = 20 THEN '2'                WHEN #{search_option.keyword} = 11 THEN '3'                WHEN #{search_option.keyword} = 21 THEN '4'                END                )            </if>  
            <if test="search_option.code == 2">  
                AND dept_nm LIKE CONCAT('%', #{search_option.keyword}, '%')  
            </if>  
            <if test="search_option.code == 3">  
                AND emp_nm LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 4">  
                AND grade LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 6">  
                AND subject_nm LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 7">  
                AND project_nm LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
            <if test="search_option.code == 8">  
                AND company_name LIKE CONCAT('%',  #{search_option.keyword} , '%')  
            </if>  
        </foreach>  
    </if>  
    <if test="college_name != null">  
        AND college_name = #{college_name}  
    </if>  
    ) C  
    ORDER BY C.yy DESC, C.term DESC    <if test="page != null and count != null">  
        LIMIT #{page}, #{count}  
    </if>  
</select>
```

- 위의 코드는 MyBatis XML Mapper 파일에서 정의된 SQL 쿼리이다.
	- 이 쿼리는 `selectCapForStudent`라는 ID를 가지며, `capResultMap`이라는 결과 매핑을 사용하여 쿼리의 결과를 매핑한다.

- 해당 쿼리는 두 개의 서브쿼리를 UNION ALL로 결합하여 결과를 반환한다.
	- 첫 번째 서브쿼리는 `vsquare_subject_score`, `tb_edit_cap_stone_design`, `tb_previous_participation_department_name`, `tb_participation_department`, `tb_cap_stone_design_lecture`, `tb_cap_stone_design_delete`, `vsquare_stu_inf`, `v_capston_member`, `v_capston_result` 테이블들과 조인하여 결과를 가져온다.

	- 두 번째 서브쿼리는 `tb_add_cap_stone_design` 테이블에서 데이터를 가져온다.
	- 두 서브쿼리의 결과를 UNION ALL로 결합하고, 그 결과를 정렬하여 최종적으로 반환한다.

- 이 쿼리는 주어진 조건에 따라 학생들의 프로젝트 정보를 검색하고, 그 결과를 반환하는 역할을 한다. MyBatis를 사용하여 SQL 쿼리를 XML 파일에 정의함으로써, Java 코드에서 이 쿼리를 호출하여 데이터베이스와 상호작용할 수 있다.

- 또한, 위에서 `id`와 `resultMap`이라는 속성이 있다.
	- `id`는 매퍼 XML 파일에서 해당 쿼리를 식별하는 고유한 이름이다. 
	- 이를 통해 Java 코드에서 매퍼 XML 파일에 정의된 쿼리를 호출할 수 있다.
	- 여기서는 `selectCapForStudent`라는 이름을 사용하여 해당 쿼리를 식별한다.
		- 즉, 매퍼 인터페이스 파일인 `PortalMapper.java`의 `selectCapForStudent()` 이름과 매핑된 것이다.
		- 실제로, 매퍼 인터페이스에 정의된 메서드와 동일한 이름으로 설정해야 한다
    
- `resultMap`은 쿼리의 실행 결과를 매핑하는 데 사용되는 resultMap의 이름이다.
	- 이 resultMap은 동일한 매퍼 XML 파일 내에 정의된 `<resultMap>` 요소를 참조하여 결과 매핑을 수행한다.
	- 결과 매핑은 쿼리 결과의 각 열을 자바 객체의 필드에 매핑하는 것을 의미한다.
	- 여기서는 `resultMap="capResultMap"` 부분에서, 매퍼 XML 파일에서 쿼리 실행 결과를 매핑하는데 사용되는 `capResultMap`이라는 이름의 resultMap을 참조합니다

- `resultMap`은 SQL 쿼리 결과를 자바 객체로 변환하는 방법을 정의한다. 
	- 즉, 쿼리의 각 열을 자바 객체의 필드에 매핑하는 역할을 한다. 이것은 쿼리 결과를 자바 객체로 변환하여 반환할 때 사용죈다.

---

```xml
<resultMap type="com.vsquare.carina.project.model.CapStu" id="capResultMap"> ... </resultMap>
```
- 먼저 이 부분을 보자.
	1. `type`
		- `type="com.vsquare.carina.project.model.CapStu"`는 resultMap이 매핑할 자바 객체의 타입을 지정하는 것이다.
		- 매핑할 자바 객체의 풀 패키지 경로에 해당하는 클래스로, 이 클래스의 객체는 SQL 쿼리 결과의 각 행을 표현하는 데 사용된다.
		- `<resultMap>` 요소는 SQL 쿼리 결과 열과 자바 객체의 필드를 매핑하는 데 사용된다. 이 때, `type` 속성에 지정된 자바 클래스는 SQL 쿼리 결과를 매핑하기 위한 대상 자바 객체의 타입을 나타낸다.
		- 따라서 위의 코드에서 `type="com.vsquare.carina.project.model.CapStu"`는 `capResultMap`이라는 resultMap이 `com.vsquare.carina.project.model.CapStu` 클래스의 객체에 매핑될 것임을 나타낸다.
			- 결과적으로 SQL 쿼리 결과는 `CapStu` 클래스의 객체로 변환되어 반환될 것이다.
	2. `id`
		- `id`는 resultMap을 식별하는 고유한 이름을 지정하는 속성이다.
		- 이 이름은 해당 resultMap을 참조할 때 사용된다. 여기서는 `id="capResultMap"`으로 설정되어 있으므로, 다른 부분에서 이 resultMap을 참조할 때 이 이름을 사용하여 지정한다.
		- 예를 들어 쿼리의 `resultMap` 속성에서 이 이름을 사용하여 resultMap을 지정할 수 있다.
		- `<select id="selectCapForStudent" resultMap="capResultMap">...</select>`에서의 `resultMap` 속성이 이 부분의 `id`와 같은 것이다.


- 다음은 전체 코드이다.
```xml
<resultMap type="com.vsquare.carina.project.model.CapStu" id="capResultMap">  
    <id property="clubMngSeq" column="club_mng_seq"/>  
    <result property="yy" column="yy"/>  
    <result property="term" column="term"/>  
    <result property="collegeName" column="college_name"/>  
    <result property="deptNm" column="dept_nm"/>  
    <result property="studId" column="stud_id"/>  
    <result property="studNm" column="stud_nm"/>  
    <result property="stuGrade" column="stu_grade"/>  
    <result property="empNm" column="emp_nm"/>  
    <result property="subjectNo" column="subject_no"/>  
    <result property="subjectNm" column="subject_nm"/>  
    <result property="projectNm" column="project_nm"/>  
    <result property="projectType" column="project_type"/>  
    <result property="projectTypeCode" column="project_type_code"/>  
    <result property="grade" column="grade"/>  
    <result property="mark" column="mark"/>  
    <result property="companyNm" column="company_nm"/>  
</resultMap>
```

- 이 코드는 MyBatis XML Mapper 파일에서 결과 매핑을 정의한 부분이다.
	- 해당 코드는 `com.vsquare.carina.project.model.CapStu` 클래스에 대한 결과 매핑을 정의하고 있습니다.
	- `<resultMap>` 태그는 결과 매핑을 정의할 때 사용되며, 여기서는 `capResultMap`이라는 이름의 resultMap을 정의하고 있다.

- 두 가지 태그가 보이는가? `<id>`와 `<result>`말이다.
	- `<id>` 태그는 주요 식별자(primary key)를 나타내는데 사용되며, 해당 클래스에서는 `clubMngSeq`를 주요 식별자로 사용하고 있다.
	- `<result>` 태그는 데이터베이스 열과 자바 객체의 속성을 매핑하는데 사용된다.
		- 각각의 `<result>` 태그는 데이터베이스의 열 이름과 매핑될 자바 객체의 속성을 지정한다.

- 그리고 각 태그에는 `property`와 `column` 속성이 보인다.
	- `property`는 resultMap에서 매핑된 자바 객체의 필드 이름을 지정하는 속성이다.
		- 이 속성은 SQL 쿼리 결과의 각 열을 매핑할 때 사용된다. 
		-  `<result property="deptNm" column="dept_nm"/>`에서, property는 자바 객체로서의 프로퍼티명이다.

	- `column`은 SQL 쿼리 결과의 열 이름을 지정하는 속성이다.
		- 여기서 `column="yy"`는 resultMap에서 매핑할 SQL 쿼리 결과의 열 이름을 지정한다. 
		- 이 속성은 SQL 쿼리 결과의 열을 자바 객체의 필드에 매핑할 때 사용된다.
		-  `<result property="deptNm" column="dept_nm"/>`에서, column은 데이터베이스의 column명이다.
	
-  `<result property="deptNm" column="dept_nm"/>`는 데이터베이스에 있는 `dept_nm` column은 자바 객체로 내보내지는 매핑과정에서 `deptNm`이 된다는 의미이다.