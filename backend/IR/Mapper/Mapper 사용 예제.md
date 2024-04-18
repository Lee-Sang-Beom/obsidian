
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

- 이 코드는 MyBatis XML Mapper 파일에서 결과 매핑을 정의한 부분입니다.
	- 해당 코드는 `com.vsquare.carina.project.model.CapStu` 클래스에 대한 결과 매핑을 정의하고 있습니다.
	- `<resultMap>` 태그는 결과 매핑을 정의할 때 사용되며, 여기서는 `capResultMap`이라는 이름의 resultMap을 정의하고 있습니다.

- `<id>` 태그는 주요 식별자(primary key)를 나타내는데 사용되며, 해당 클래스에서는 `clubMngSeq`를 주요 식별자로 사용하고 있습니다.
- `<result>` 태그는 데이터베이스 열과 자바 객체의 속성을 매핑하는데 사용됩니다. 각각의 `<result>` 태그는 데이터베이스의 열 이름과 매핑될 자바 객체의 속성을 지정합니다.

- 예를 들어, `<result property="yy" column="yy"/>`는 `yy` 열의 값을 자바 객체의 `yy` 속성에 매핑합니다.

- 이와 같이 `<resultMap>`을 사용하여 데이터베이스 결과를 자바 객체에 매핑할 수 있습니다. 이러한 매핑을 통해 SQL 쿼리 결과를 자바 객체로 변환하여 사용할 수 있습니다.

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